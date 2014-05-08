ebs-to-instance-store-ami
=========================

Some random code related to converting EBS AMI into instance store AMI.


Requirements
------------

* Ansible 1.5+
* boto
* relevant SSH keys added to SSH agent or configured in `~/.ssh/config`


Usage
-----

1. Copy `secrets.example.yml` to `secrets.yml` and fill in the gaps.
2. Run `ansible-playbook -i inventory all.yml`.
3. TODO...

Example output:

```
ansible-playbook -i inventory all.yml

PLAY [localhost] **************************************************************

TASK: [launch EBS-backed instance] ********************************************
changed: [localhost]

TASK: [wait for SSH to come up] ***********************************************
ok: [localhost]

TASK: [add instance public IP to host group] **********************************
ok: [localhost]

PLAY [ec2] ********************************************************************

GATHERING FACTS ***************************************************************
ok: [54.211.107.72]

TASK: [mount ephemeral drive] *************************************************
changed: [54.211.107.72]

TASK: [create temporary directory] ********************************************
changed: [54.211.107.72]

TASK: [upload certificate] ****************************************************
changed: [54.211.107.72]

TASK: [upload private key] ****************************************************
changed: [54.211.107.72]

TASK: [install packages] ******************************************************
changed: [54.211.107.72] => (item=ruby,ntp,grub)

TASK: [clock sync] ************************************************************
ok: [54.211.107.72]

TASK: [enable ntpd] ***********************************************************
changed: [54.211.107.72]

TASK: [copy kernel, initramfs and grub.conf into canonical places] ************
changed: [54.211.107.72] => (item={'src': '/boot/boot/vmlinuz-*', 'dst': '/boot'})
changed: [54.211.107.72] => (item={'src': '/boot/boot/initramfs-*.img', 'dst': '/boot'})
changed: [54.211.107.72] => (item={'src': '/boot/boot/grub/grub.conf', 'dst': '/boot/grub/grub.conf'})

TASK: [download ec2-ami-tools] ************************************************
changed: [54.211.107.72]

TASK: [extract ec2-ami-tools] *************************************************
changed: [54.211.107.72]

TASK: [create AMI bundle directory] *******************************************
changed: [54.211.107.72]

TASK: [generate AMI bundle] ***************************************************
changed: [54.211.107.72]

TASK: [upload AMI bundle] *****************************************************
changed: [54.211.107.72]

TASK: [register AMI] **********************************************************
changed: [54.211.107.72]

TASK: [display AMI ID] ********************************************************
ok: [54.211.107.72] => {
    "msg": "IMAGE\tami-f4c0239c"
}

PLAY [localhost] **************************************************************

TASK: [destroy instance] ******************************************************
changed: [localhost]

PLAY RECAP ********************************************************************
54.211.107.72              : ok=16   changed=13   unreachable=0    failed=0
localhost                  : ok=4    changed=2    unreachable=0    failed=0
```


Under the hood
--------------

1. Launch a new host instance based on EBS AMI
2. Slightly customize it and rebundle, upload and register new a instance store AMI
3. Destroy host instance


Known issues
------------

1. You have to manually increment version number in `ami_name` variable.
2. AMI ID output is not pretty.
