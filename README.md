# r-series-ansible-gtm-ltm

git check-ignore **/*

rpm command must be avialable on the Ansible host.  It's included in Linux, but not MacOS

For MacOS:

This is the supposed fix, but didn't work for me.

    brew tap-new archer/archtap
    brew tap homebrew/core
    brew extract --version=4.18.1 rpm archer/archtap
    brew install archer/archtap/rpm@4.18.1
    brew edit $USER/rpm-tap/rpm@4.18.1

This WORKED for me https://nelson.cloud/how-to-install-older-versions-of-homebrew-packages/

Find rpm.rb instead of terraform.rb in the example.


On MAC need to run this command or add to shell bashrc/zshrc file:  https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#running-on-macos-as-a-control-node
 export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES


Requires python libraries installed on Ansible host.
    pip install netaddr
    pip install jmespath

    




Try to create device trust
Dec  5 22:33:30 ltm21.f5.com notice tmm[10991]: 01010201:2: Re-enabling general logging; held 26 messages.
Dec  5 22:33:30 ltm21.f5.com crit tmm[10991]: 01010201:2: Inet port exhaustion on 10.0.2.103 to 10.0.2.104:443 (proto 6)
Dec  5 22:33:30 ltm21.f5.com err devmgmtd[8509]: 015a0000:3: iControl connection to 10.0.2.104 failed
Dec  5 22:33:30 ltm21.f5.com err devmgmtd[8509]: 015a0000:3: iControl connection to 10.0.2.104 failed
Dec  5 22:34:21 ltm21.f5.com crit tmm[10991]: 01010201:2: Inet port exhaustion on 10.0.2.103 to 10.0.2.104:443 (proto 6)
Dec  5 22:34:21 ltm21.f5.com err devmgmtd[8509]: 015a0000:3: iControl connection to 10.0.2.104 failed
Dec  5 22:34:21 ltm21.f5.com err devmgmtd[8509]: 015a0000:3: iControl connection to 10.0.2.104 failed
Dec  5 22:36:25 ltm21.f5.com crit tmm[10991]: 01010201:2: Inet port exhaustion on 10.0.2.103 to 10.0.2.104:443 (proto 6)
Dec  5 22:36:25 ltm21.f5.com err devmgmtd[8509]: 015a0000:3: iControl connection to 10.0.2.104 failed
Dec  5 22:36:25 ltm21.f5.com err devmgmtd[8509]: 015a0000:3: iControl connection to 10.0.2.104 failed