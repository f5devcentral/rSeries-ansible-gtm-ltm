# r-series-ansible-gtm-ltm

## IMPORTANT NOTE: Declarative Onboarding is not yet working for LTM HA.  GTM is working correctly.  Just remove LTMs from the inventory file.

To run playbook, first create `inventory` file, based on the structure shown in `inventory.example`.  Then use the command:
- `ansible-playbook -i inventory deploy-rseries-tenant.yaml`


`rpm` command must be avialable on the Ansible host.  It's included in Linux, but not MacOS

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


Requires python libraries installed on Ansible host:
- `pip install netaddr`
- `pip install jmespath`