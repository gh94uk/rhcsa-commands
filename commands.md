**File Systems**
```
Find files owned by no user or group and do not show errors
find / -nouser -nogroup 2> /dev/null
Mount device by UUID (manual)
mount UUID="X" /mountpoint
Mount device by UUID (fstab)
UUID="X"   /mountpoint   ext4    defaults    0 2
List user's open files
lsof -u username
Create a symbolic link (soft)
ln -s source destination
Create a symbolic link (hard)
ln source destination
```

**Users and Groups**
```
Lock/Unlock user account
usermod -L/-U username
Add user to group(s)
usermod -aG group username
Change user's primary group
usermod -g group username
Create group with custom Group ID
groupadd -g 1050 groupname
Create custom system group
groupadd -r groupname
Force user password change upon next logon
chage -d 0 username
Display the date in 30 days
date -d +30days
Change group ownership of file
chown :groupname filename
Add users directly to /etc/group
groupname:x:12345:user1,user2,user3
Remove user from group
gpasswd -d user apache
Rename the user account from bob to jim and move the home directory respectively
usermod -l jim -d /home/jim -m bob
Configure the system so that user profiles are created with an empty file called "data"
touch /etc/skel/data
```

**File Permissions and ACL's**
```
Set file permissions to allow full access
chmod 777 file
Display umask in symbolic form
umask -S
Ensure all new files are created with 640 permissions and directories with 750 permissions for all users
sed -i s/umask 022/umask 027/g /etc/profile
Ensure all new files for user harry are created with 777 permissions
echo 'umask 0' >> /home/harry/.bash_profile
Save ACL permissions to file
getfacl file > aclperms
Set ACL permissions reading from a file
getfacl file1 | setfacl --set-file=- file2
Revoke all ACL permissions on a file
setfacl -b file
Grant the special bit group permissions on a file
chmod g+s filename
Recursively set the ACL permissions to grant all groups Read and Write permissions on a directory
setfacl -R -m g::rw /directory
Set the default ACL permissions to grant user1 Read, Write and Execute permissions on a directory
setfacl -d -m u:user1:rwx /directory
Prevent all users and groups from writing to file1 using ACLs
setfacl -m mask::rx file1
Grant the devs group Read, Execute permissions on a directory.  Ensure this change applies to files already with execute permissions.
setfacl -Rm g:devs:rX /directory
Prevent users who are not the owner from deletion of files in a directory
chmod +t /directory
Set the file permissions on file1 to have no group or owner
chown nobody:nobody file1
Which permission bit does a user need on a folder in order to cd into it?
Execute
What are the default permissions for files and folders?
Files: 644   Folders: 775
Starting point:  Files: 666   Folders: 777
Which file would you use to set the umask permanently?
/etc/profile
```

**SELinux**
```
Install the package containing sealert
yum install setroubleshoot
Change the SELinux mode to permissive
setenforce permissive
List locally configured SELinux booleans
semanage boolean -l -C
Query the audit daemon logs for AVC denials since 9AM
ausearch -ts 09:00 | grep AVC
Lookup SELinux alert by ID
sealert -l x-x-x-x
Change the SELinux type context of the /etc/ folder and all files/folders within to etc_t
semanage fcontext -a -t etc_t '/etc(/.*)?`
```

**Process Management and Scheduling**
```
Show jobs
jobs -l
Kill the second job
kill %2
Stop a job
kill -SIGSTOP PID
View all processes
ps -ef
Run a command in the background with a custom nice level
nice -n 15 command &
Apply a custom nice level to a process
renice -n -19 PID
Find number of processors
nproc
grep "processor" /proc/cpuinfo | wc -l
```

**System and Package Management**
```
What is the man page relating to configuring yum repositories
man yum.conf
Add a repository
yum-config-manager --add-repo=file/url
Enable a repository
yum-config-manager --enablerepo=repoid
List the repositories
yum repolist
Downgrade RedHat to the previous release
yum downgrade redhat-release
Upgrade the kernel
yum update kernel
Generate initramfs (/boot) to support the latest kernel
dracut -v --force  
Clean yum
yum clean all
List timezones
timedatectl list-timezones
Set the timezone to Amsterdam
timedatectl set-timezone Europe/Amsterdam
List locales
localectl list-locales
Set the locale to France
localectl set-locale LANG=fr_FR.utf8
Show hidden group packages in yum
yum group list hidden
Update the manual pages database
mandb
Show the first field of /etc/passwd using the cut utility and sort alphabetically
cut -f 1 -d : /etc/passwd | sort -d
Change all characters to uppercase in file1
tr [a-z] [A-Z] file1
Find all lines in /etc/auto.master which are not comments
grep -v '^#' /etc/auto.master
Print line 2 from file1
sed -n 2p file1
Delete line 4 from file2
sed -i -e '4d' file2
Replace all instances of user1 with user2 in /etc/passwd
sed s/user1/user2/g /etc/passwd
Print the UID from the /etc/passwd file on the line containing user1
awk -F : '/user1/ { print $3 } /etc/passwd'
Change to the virtual terminal tty4
chvt 4
Query the custom 'downloads' repository
repoquery downloads
Download all packages that begin with q
yumdownloader q*
List the information relating to the cdrom kernel module
modinfo cdrom
Load the fat kernel module
modprobe fat
Ensure the autoeject option is set to 1 on the cdrom kernel module
echo ‘options cdrom autoeject=1’ > /etc/modprobe.d/cdrom.conf
Temporarily disallow icmp echo requests (pings)
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
Increase the swappiness to 50 using kernel modules
echo vm.swappiness=50 > /etc/sysctl.d/50-swap.conf
Where can you find some parameters relating to kernel modules?
/sys/module/modulename/parameters
Reload the systemd configuraton
systemctl daemon-reload
```

**Service Management, Boot Loader and Troubleshooting**
```
Disable a service
systemctl mask servicename
Switch graphical environment during runtime
systemctl isolate desired.target
Set graphical environment as default
systemctl get-default graphical.target
Reset the root password
Append rd.break to the linux16 line
mount -o remount,rw /sysroot
chroot /sysroot
passwd root
touch /.autorelabel
Enable the debug shell
systemctl enable debug-shell
Perform a file system check
fsck
Make permanent changes to the grub2 boot loader
grub2-mkconfig -o /boot/grub2/grub.cfg
Reinstall the boot loader
grub2-install
Display the grub2-install info
info grub2-install
Install the package for probing operating systems
apt install os-prober
Update GRUB after making changes
update-grub
Disable the operating system prober
echo 'GRUB_DISABLE_OS_PROBER=true' >> /etc/default/grub
```

**Remote Access and SSH**
```
Copy your public RSA SSH key to server1.example.com
ssh-copy-id -i ~/.ssh/id_rsa.pub server1.example.com
Generate a 4096 bit RSA SSH key
ssh-keygen -t rsa -b 4096
Start the SSH-Agent in the background
eval $(ssh-agent -s)
Add your RSA private key to SSH-Agent
ssh-add ~/.ssh/id_rsa
```

**Networking**
```
Show the network connections
nmcli con show
Show the network devices' status
nmcli dev status
Create a static Ethernet connection with IP 192.168.1.10, gateway 192.168.1.254
nmcli con add con-name "eth0" ifname eth0 type ethernet ip4 192.168.1.10/24 gw4 192.168.1.254
Add the DNS server 192.168.1.20 to the eth0 connection
nmcli con mod "eth0" ipv4.dns 192.168.1.20
Add an additional DNS server with IP 8.8.8.8 to the eth0 connection
nmcli con mod "eth0" +ipv4.dns 8.8.8.8
Activate the eth0 connection
nmcli con up "eth0"
Verify the default gateway
ip route
Delete the default route of 192.168.1.254
ip route del default via 192.168.1.254
```
**System Logging and NTP**
```
View sudo command logs
cat /var/log/secure
Follow the messages log file
tail -f /var/log/messages
Enable persistent journald logging
mkdir -m 2775 -p /var/log/journal
chown :systemd-journal /var/log/journal
killall -USR1 systemd-journald
Show journald logs from the previous boot
journalctl -b-1
Show kernel logs
dmesg
Enable the service to monitor user activity
systemctl enable psacct
Show the last logged in users
last
Set the system to use NTP
timedatectl set-ntp yes
Set the hardware clock to use the current system time
hwclock --systohc
```

**Logical Volumes and Disk Management**
```
Create a new 100MB primary partition (MBR)
fdisk /dev/sda > n > p > partition no. > 100M > w
Set the partition type to Linux LVM (MBR)
fdisk /dev/sda > t > partition no. > 83 > w
Create a new 2GB swap partition (GPT)
gdisk /dev/sda > n > partition no. > 2G > 8200 > w
Create a physical volume called pv01 using /dev/vda1 and /dev/vda2
pvcreate -n pv01 /dev/vda1 /dev/vda2
Create a 4GB volume group called vg01 using /dev/vda1 and /dev/vda2
vgcreate vg01 -L 4G /dev/vda1 /dev/vda2
Create a 2GB logical volume called lv01 using the volume group vg01
lvcreate -n lv01 -L 2G vg01
Create an XFS filesystem on the logical volume logvol1 with the label data
mkfs.xfs -L data /dev/mapper/vg01-lv01
Initiate a kernel re-read of the new partition table
partprobe
Increase xfs device storage by 500MB
xfs_growfs /mountpoint -D +500M
Apply swap signature to partition
mkswap /dev/sda1
Activate swap space
swapon /dev/sda1
Set the priority to -1 for the swap device /dev/mapper/rhel-swap
/dev/mapper/rhel-swap   swap   swap   defaults,pri=-1   0 0
Add partition /dev/sda3 to the volume group vg01
vgextend vg01 /dev/sda3
Increase the lv01 logical volume size by 1GB and it’s file system respectively
lvextend -r -L +1G /dev/mapper/vg01-lv01
Extend the lv01 volume group by all of the free space available
lvextend -l +100%FREE /dev/mapper/vg01-lv01
```

**Authentication and Network File Systems**
```
Install the required packages for network authentication
yum -y install authconfig-gtk ipa-client nss-pam-ldapd
Configure authentication via the CLI
authconfig-tui
Configure authentication via the GUI
authconfig-tui / system-config-authentication
Find password hashing algorithm
authconfig --test | grep hashing
Change password hashing algorithm to sha512
authconfig --passalgo=sha512
Check user entry in passwd database
getent passwd username
Discover/Join Kerberos server
realm discover/join server.example.com
Enable logging in via Active Directory (AD)
realm permit --realm server.example.com --all
Authenticate with IPA server using server1.example.com, ensuring home directories are created and NTP isn't required
ipa-client-install --domain=server1.example.com --no-ntp --mkhomedir
Check a user exists on the IPA server
getent passwd username
Create an indirect autofs mapping
vi /etc/auto.master.d/indirect.autofs
/mountpoint   /etc/auto.indirect   --timeout=60
vi /etc/auto.indirect
*    rw,sync,root_squash,no_subtree_check   servername:/mountpoint/&  
Create a direct autofs mapping
vi /etc/auto.master.d/direct.autofs   --timeout=60
/-   /etc/auto.direct
vi /etc/auto.direct
/mountpoint   rw,sync,root_squash,no_subtree_check   servername:/mountpoint
Create an indirect autofs CIFS mapping
vi /etc/auto.direct
/mountpoint   -fstype=cifs,credentials=file,uid=username,noserverino,vers=2.0   servername:/mountpoint
List available CIFS shares
smbclient -L servername
Show available mount points on a NFS server
showmount -e servername
Manually mount the NFS share "sales" on server1.example.com to /sales
mount -t nfs server2.example.com:/sales /sales
Which fstab option is used to ensure a networked share only starts on boot when the network is available?
_netdev
Manually mount the CIFS share "data" on server2.example.com to /data with the username pete
mount -t cifs -o username=pete //server1.example.com:/data /data
```

**Firewall Configuration**
```
List all firewall rules
firewall-cmd --list-all
Permanently allow http and https through the firewall in the public zone
firewall-cmd --permanent --add-service={http,https} --zone=public
Set the default zone to DMZ
firewall-cmd --set-default-zone=dmz
Permanently allow port 8080 through the firewall
firewall-cmd --permanent --add-port=8080/tcp
Allow the 172.16.1.0/24 network through the firewall to access the 10.1.0.99 server via ports 8080-8090
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address=172.16.1.0/24 destination address=10.1.0.99/32 port port=8080-8090 protocol=tcp accept'
Activate masquerade in the desired zone
firewall-cmd --zone=public --add-masquerade
Permanently forward port 22 onto port 2222
firewall-cmd --permanent --zone="public" --add-forward-port=port=22:proto=tcp:toport=2222
```

**Virtualization and Kickstart**
```
Check if the server supports virtualization
egrep 'vmx|svm' /proc/cpuinfo
Check the kvm kernel module exists
lsmod | grep kvm
Install the kickstart validator tool
yum install pykickstarter
View the kickstart documentation
cat /usr/share/doc/pykickstart-*/kickstart-docs.rst
Verify the differences in kickstart from RHEL6 to RHEL7
ksverdiff -f RHEL6 -t RHEL7
View the system's auto-generated kickstart file
cat /root/anaconda-ks.cfg
Install the packages required for virtualization
yum group install "Virtualization Host" "Virtualizaton Client"
Install the individual packages required for virtualization
yum install kvm libvirt virt-manager qemu-kvm -y
Convert a vmdk file to a qcow2 file
qemu-img convert -O file.vmdk file.qcow2
```
