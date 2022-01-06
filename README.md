# cloud-init-autoinstall
Cloud-init user-datas for autoinstalls.

## Storage  

### Initializing disk  

   *disk_name:    Example: /dev/sda*  
   *grub_device:  True if the bootloader is on this disk.*  
   *disk-id:      ID that the disk will be identified by along the rest of the install.*  

```
- ptable: gpt
    path: /dev/<disk_name>
    wipe: superblock-recursive
    preserve: false
    name: ''
    grub_device: <true/false>
    type: disk
    id: <disk-id>
```

### Installing with EFI  

   *partition-id:   ID that the partition will be identified by along the rest of the install.*  
   *formatted-id:   ID that the formatted partition will be identified by along the rest of the install.*  

EFI  
```
- device: <disk-id>
    size: 500M
    wipe: superblock
    flag: boot
    number: <partition-id>
    preserve: false
    grub_device: true
    type: partition
    id: <partition-id>
  - fstype: fat32
    volume: <partition-id>
    preserve: false
    type: format
    id: <formatted-id>
```

PUT THIS AT THE END OF THE STORAGE CONFIG!
```
- device: <formatted-id>
        path: /boot/efi
        type: mount
        id: <mounted-id>
```

### Installing with BIOS (Legacy)

Grub  
```
  - device: <disk-id>
    size: 1M
    flag: bios_grub
    number: 1
    preserve: false
    grub_device: false
    type: partition
    id: <partition-id>
```

/boot  
```  
  - device: <disk-id>
    size: 1G
    wipe: superblock
    flag: linux
    number: 2
    preserve: false
    grub_device: false
    type: partition
    id: <partition-id-2>
  - fstype: ext4
    volume: <partition-id-2>
    preserve: false
    type: format
    id: <formatted-id>
```
PUT THIS AT THE END OF THE STORAGE CONFIG!
```
- device: format-boot
    path: /boot
    type: mount
    id: mount-boot
```

## Late-commands

### Root login with password  

```
- sed -i 's|^root:.:|root:<encrypted_password>:|' /target/etc/shadow
- "sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /target/etc/ssh/sshd_config"
```

### Root login with ssh key  

```
- "mkdir -p /target/root/.ssh"
- "echo '<key>' > /target/root/.ssh/authorized_keys2"
```
