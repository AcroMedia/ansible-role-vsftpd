# acromedia.ansible-role-vsftpd

![.github/workflows/molecule.yml](https://github.com/AcroMedia/ansible-role-vsftpd/workflows/.github/workflows/molecule.yml/badge.svg)

## Requirements

OS:
- Ubuntu 16.04+ or RedHat/CentOS 6+

Other:
- Your playbook must gather facts
- Server runs from behind a firewall
- FTP over TLS must be used. No clear text connections allowed.
- No anonymous activity allowed
- Only explicitly defined users are allowed
- All ftp connections are chrooted and separate from each other
- Base directory in a user's ftp root does not allow write access (this is a chroot side effect). The 'files' subdirectory inside it does.
- FTP users are real linux users, not virtual users.

## Example playbook
```yaml
- hosts: ftp-nodes
  become: true
  gather_facts: true
  vars:
    vsftpd_pasv_min_port: '11000'
    vsftpd_pasv_max_port: '11999'
    vsftpd_pasv_address: 8.8.8.8   #  The public IP of your server.
    vsftpd_rsa_cert_file: /path/to/your/fullchain.pem
    vsftpd_rsa_private_key_file: /path/to/your/privkey.pem
    vsftpd_ini_config_extras: []  #  For anything additional config not already handled by the role. See defaults.yml.
    vsftpd_users:
     - username: ftptester
       password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          6462343764643764383.....etc.ete.cetc
       update_password: always   # Or `on_create`. Only exposed here to allow idempotence tests to pass. Since the salt changes every run, the `user` task always shows as changed, even if the password didn't.
  roles:
    - name: Install vsftpd
      role: custom/acromedia.vsftpd
```
