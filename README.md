# f5 rSeries Example Ansible Deployment of GTM Sync-Group and LTM HA PAir, WIPs and VSs

This example Ansible playbook deploys the following resources using a series of Ansible Roles:

- A pair of GTM tenants, deployed onto two separate rSeries platforms.
- A pair of LTM tenants, deployed onto two separate rSeries platforms.
- On first deployment only, the default password is updated on the deployed tenants.  Future playbook runs ignore this for any tenants that were previously deployed.
- Installs the ILX packages and checks for correct installation.  Thanks to @simonkowallik for the code.
- Creates DO declaration from a tenmplate using the inventory.  DO also includes replacement device certificates.
  - The template also handles creation of LTM HA pairs and LTM standalone devices, based on the inventory.
- Builds the GTM sync-group, creates the DC and GTM Server objects and swaps device certificates to allow the trust to form (gtm_add command).
- Adds LTMs to the GTM sync-group as 'Servers' and and swaps device certificates to allow the trust to form (bigip_add command).
- Deploys application LTM Virtual Servers based on inventory structure
- Creates GTM WIPs, with pools.  It includes adding the Virtual Servers to the relevant parent Server object, for the pool to consume.


## Running the playbook

To run playbook, first create `inventory` file, based on the structure shown in `inventory.example`.  Then use the command:
- `ansible-playbook -i inventory playbook.yaml`

## Inventory

Ansible best practice (https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html) suggests using a folder structure for vars, but in this example I have added all variables to the inventory file, which makes it easier to understand the structre at first glance.  

The inventoy structure is important, becasue GTMs and LTMs have different deployment architectures and requirements and these requirements change during the course of the playbook as the deployment configuration advances.

> **_IMPORTANT:_**  You MUST define the GTM sync-group primary device in two locations within the inventory.  Firstly, set `gtm_sync_group_primary: true` variable within the chosen gtm host configuration (this is set to `gtm1.f5.com` in the example inventory).  Secondly, add the hostname of the chosen gtm within the `gtm_sync_group_primary` group (this is set to the host `gtm1.f5.com` in the example inventory)

As an example, for a GTM deployment, initially, the ansible_host is the rSeries F5OS hypervisor, but then it changes to the GTM management IP.  LTMs are initally single devices, but after DO, we optionsllay need to refer to them in pairs.  

We also need a structure for the application deployments of Virtual Servers (`tenants` variable nested inside the HA primary or stand-alone LTM hosts) and WIPs (`wips` nested inside the gtm host object that has been set as primary with `gtm_sync_group_primary: true` variable).




## Creating device certificates 

You can find example certifictaes in the correct location already, but if you want to generate your own, you can use the following commands, substituting the variables with your chosen values: 

### Create a CA

#### Generate a random
openssl rand -out ./ca/.rand 2048

#### Create a key
openssl genrsa -rand ./ca/.rand -out ./ca/client-ca.key 2048

#### Create the crt
openssl req -x509 -new -key ./ca/client-ca.key -out ./ca/client-ca.crt -days 825


### Create CSR and key
openssl req -out ./${shortname}/${shortname}.csr -newkey rsa:2048 -nodes -keyout ./${shortname}/${shortname}.key -config ./${shortname}/san.conf -subj "/C=GB/ST=London/L=London/O=F5/OU=Demo/CN=${cn}"

### Create san.conf file
Create a san.conf file using the following structure, replaceing the example values with your own:

`
  [ req ]
  default_bits       = 2048
  distinguished_name = req_distinguished_name
  req_extensions     = req_ext
  [ req_distinguished_name ]
  countryName                 = GB
  stateOrProvinceName         = London
  localityName               = London
  organizationName           = F5
  commonName                 = f5-1.f5demo.net
  [ req_ext ]
  extendedKeyUsage = clientAuth, serverAuth
`

The following command references the san.conf you create.


### Sign CSR
openssl x509 -req -in ${shortname}.csr -out ${shortname}.crt -CAkey ./ca/client-ca.key -CA ./ca/client-ca.crt -days 365 -CAcreateserial -CAserial serial -extensions req_ext -extfile ./${shortname}/san.conf


## Prerequisites 

Requires python libraries installed on Ansible host:
- `pip install netaddr`
- `pip install jmespath`


The `rpm` command must be avialable on the Ansible host, in order to install the ILX packages.  It's included in Linux, but not MacOS

For MacOS:

This is the supposed fix, but DID NOT work for me.

    brew tap-new archer/archtap
    brew tap homebrew/core
    brew extract --version=4.18.1 rpm archer/archtap
    brew install archer/archtap/rpm@4.18.1
    brew edit $USER/rpm-tap/rpm@4.18.1

This WORKED for me https://nelson.cloud/how-to-install-older-versions-of-homebrew-packages/
- Find rpm.rb instead of terraform.rb in the example.

On MAC need to run this command or add to shell bashrc/zshrc file:  https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#running-on-macos-as-a-control-node
- `export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES`