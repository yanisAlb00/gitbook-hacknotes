# NFS

NFSv4 supports Kerberos for user authentication.

NFS is based on the Open Network Computing Remote Procedure Call (ONC-RPC/SUN-RPC) protocol exposed on TCP and UDP ports 111.
Only one UDP or TCP port 2049 is used to run the service

The NFS protocol has no mechanism for authentication or authorization. 

# Configuration

/etc/exports :  table of physical filesystems on an NFS server accessible by the clients

echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
systemctl restart nfs-kernel-server 
exportfs

## Interesting parameters 

rw : Read and write permissions.
insecure : Ports above 1024 will be used.
nohide : If another file system was mounted below an exported directory, this directory is exported by its own exports entry.
no_root_squash : All files created by root are kept with the UID/GID 0.

if the root_squash option is set, we cannot edit the backup.sh file even as root.

# Commands

# Footprinting the service

## Enumeration

sudo nmap $TARGET -p111,2049 -sV -sC
sudo nmap --script nfs* $TARGET -sV -p111,2049

## Interaction

showmount -e $TARGET
    Export list for 10.129.14.128:
    /mnt/nfs 10.129.14.0/24
mkdir target-NFS
sudo mount -t nfs $TARGET:/ ./target-NFS/ -o nolock
cd target-NFS
tree .
cd ..
sudo umount ./target-NFS

ls -l mnt/nfs/
ls -n mnt/nfs/


# Escalation using NFS

If the NFS Share has the SUID of a targeted user :
- Upload a shell to the NFS Share
- Run the shell so we can have the same rights that the targeted user
