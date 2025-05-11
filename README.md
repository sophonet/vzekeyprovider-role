# Ansible role for setting up zfskeyprovider

1. Set up hosts and variables. Example for hosts file (could also be solved with host_vars)

```
primaryserver zfskey_peer=192.168.1.1:8901 zfskey_port=8901 zfs_server=True
supportserver zfskey_peer=192.168.1.2:8901 zfskey_port=8901 zfs_server=False
```

If zfs_server is true, the playbook will install the zfs-load-key.service to load zfs keys during boot time. This
is not necessary for the supportserver.

2. Example playbook

```
- name: Set up zfskeyprovider
  hosts:
    - primaryserver
    - supportserver
  roles:
    - zfskeyprovider
  remote_user: root
  vars:
    zfskeyprovider:
      key_path: "/dev/shm/zfspwd"
      private_ssl_path: "/etc/ssl/private/private_key.pem"
      private_local_ssl_path: "private_key.pem"
      private_public_ssl_path: "public_key.pem"
      zfs_key: "asd98f732234"
```

3. Run - decide which tags to apply:

  - generate_ssl_key: Generates an SSL key locally (requires openssl to be installed) - skipped if keys are already present
  - generate_encrypted_key: Encrypts the ZFS key locally using SSL key - skipped if encrypted.b64 is already present
  - upload_encrypted_key: Uploads the encrypted key (in file encrypted.b64) to web services after installation

You might want to make sure that the zfs_key in the playbook cannot be read by unauthorized persons - in case of doubt, delete it.
The same remark applies to the private SSL key - it is only required on the primary server for decrypting the ZFS key.
