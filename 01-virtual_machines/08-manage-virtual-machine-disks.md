# Azure Playground

## 01 Virtual Machines

### 08 Manage Virtual Machine Disks

**Creating and Attaching**

```bash
# 1 Attach a new disks
az vm disk attach \
    --resource-group "demo-rg" \
    --vm-name "demo-vm-1" \
    --disk "demo-disk-1"
    --new \
    --size-gb 25 \
    --sku "Premius_LRS"

# 2 Login into VM
az vm list-ip-addresses \
    --name "demo-vm-1"
    --output table

ssh -l admin W.X.Y.Z

# 3 Find the device
lsblk

# 4 Partition the disk
sudo fdisk /dev/sdc

m # for help
n # add a new partition
p # primary
1 # default number
w # write and exit

# 5 Format new partition
sudo mkfs -t ext4 /dev/sdc1

# 6 Make directory to mount
sudo mkdir /data1

# 7 Get the UUID for the device
sudo -i blkid | grep sdc1

# 8 Add the UUID to fstab
sudo vi /etc/fstab

# ie: UUID=<UUID for the device>    /data1  ext4    defaults    0 0

# 9 Mount the volume
sudo mount -a
df -h

# 10 Exit VM
exit
```

**Resizing**

```bash
# 1 Stop and deallocate the VM
az vm deallocate \
    --resource-group "demo-rg"
    --name "demo-vm-1"

# 2 Find the disk
az disk list \
    --output table

# 3 Update the disk size
az disk update \
    --resource-group "demo-rg"
    --name "demo-disk-1"
    --size-gb 100

# 4 Start the VM
az vm start \
    --resource-group "demo-rg"
    --name "demo-vm-1"

# 5 Login into VM
az vm list-ip-addresses \
    --name "demo-vm-1"
    --output table

ssh -l admin W.X.Y.Z

# 6 Comment out the UUID
sudo vi /etc/fstab

# 7 Unmount filesystem
sudo umount /data1

# 8 Expand the partition
sudo parted /dev/sdc

print # print the partition
resizepart # resisze the partition
1 # select the partition number
107GB # size of the partition
quit # exit parted

# 9 Check the filesystem
sudo e2fsck -f /dev/sdc1

# 10 Expand the filesystem
sudo resize2fs /dev/sdc1

# 11 Mount the filesystem
sudo mount /dev/sdc1 /data1

# 11 Remove commemnted out UUID
sudo vi /etc/fstab

# 12 Verify the space
df -h | grep data1
```

**Removing and Detaching**

```bash
# 1 Remove the UUID
sudo vi /etc/fstab

# 2 Unmount the disk
sudo unmount /data/1

# 3 Confirm the disk is not there
df -h | grep data1

# 4 Exit VM
exit

# 5 Detach the disk from VM
az vm disk detach \
    --resource-group "demo-rg" \
    --vm-name "demo-vm-1" \
    --name "demo-disk-1"

# 6 Delete the disk
az vm disk delete \
    --resource-group "demo-rg" \
    --name "demo-disk-1"
```

**Snapshots**

```bash
# 1 List the disks
az disk list \
    --output table | grep demo-disk-1

# 2 Update --source param. with the disk
az snapshot create \
    --resource-group "demo-rg" \
    --source "DISK_SOURCE"
    --name "demo-disk-1-snap-1"

# 3 Get list of snapshops
az snapshot list \
    --output table

# 4 Create a new disk from snapshot
az disk create \
    --resource-group "demo-rg" \
    --name "demo-disk-1-from-snap-1"
    --source "demo-disk-1-snap-1"
    --size-gb "40"

# 5 Create a VM from the new disk
az vm create \
    --resource-group "demo-rg" \
    --name "demo-vm-1-from-snap-disk-1"
    --attach-os-disk "demo-disk-1-from-snap-1"
    --os-type "Linux"

# 6 Delete snapshot
az snapshot delete \
    --resource-group "demo-rg" \
    --name "demo-disk-1-from-snap-1"
```
