When creating a snapshot or image in Linux, the UUID and PARTUUID of disk partitions are saved. Linux uses this data when mounting partitions, including the root one, so the IDs must be unique. For example, if you clone a boot disk and attach it to the same VM, two partitions with the same UUIDs appear on the VM. As a result, after starting the VM, the root partition may not be the one you specified as the boot disk.