REF: ytvideoid=WMy3OzvBWc0
Linux server - config and operations

--------------------------------------------------------------------------------------------------


--------------------------------------------------------------------------------------------------

BIOS and UEFI:

	BIOS						UEFI
 *  Basic Input Output			Unified Extensible firmware
 *  In BIOS Boot Method Disk must       In UEFI It must contains 2 partitions 
    atleast 1 partition init.	        init. One for Boot files, one for Os files.  

--------------------------------------------------------------------------------------------------

BOOTLOADERS:
GRUB ,GRUB2

grub vs grub2

	GRUB						GRUB2
 * Grub "Legacy"                               grub.cfg
 * menu.lst, grub.conf                         customizable in /etc/default/grub
 * difficult to modify                         can boot ISO, USB, UUID ,Device
 * boot menu usually displays on boot          hidden boot menu (Press shift key on boot)
 * still used by slackware linux

---------------------------------------------------------------------------------------------------

BOOT METHODS:
-* Hardware vs Software
-* PXE, USB, CD, iPXE, ISO

Hardware:        				      Software:
PXE(Preboot Execution Environment) Uses TFTP            ISO
iPXE Uses http(which is fast)                         Select kernal
USB                                                   memtest
CD
HDD

----------------------------------------------------------------------------------------------------

BOOT MODULES AND FILES:
-* Kernal
-* Boot Process
-* Modules
                                       MOD
                                        |
                                        -
  BIOS ----> GRUB  ----> vmlinux ----> Full   <------ MOD        
  UEFI       GRUB2   ^    vmlinuz       Kernal 
                    |    	         ^
                    |                    |
                   |                    MOD	
                   |
                initrd    
                          intitramfs
			--------------
		           dracut -------> Software


vmlinux   -> Base kernal
vmlinuz   -> compressed version of base linux.
initramfs -> file system on the base kernal.
initrd    -> temp kernal which loads the base kernal.

----------------------------------------------------------------------------------------------------

LOCATIONS OF KERNAL AND KERNAL MODULES:

Base kernals -> initrd, vmlinuz ---> located in /boot

Actual Kernal Modules located in /lib/modules/

KERNAL PANIC!:
-* common causes
-* what to do

Common causes:
 * Harware failures eg: ram, video cards or graphics cards, Overclocked proccessor.
 * System upgrade eg:kernal upgrade.

What to do:
 * In Grub Menu --> Select 'Advanced options' --> Select previous version successful kernal.

-----------------------------------------------------------------------------------------------------

LOADING KERNAL MODULES ON BOOT:
-* Loading
-* Blacklisting

Custom modules loading:
	Mention those Modules on the file /etc/modules

Blacklisting:
	Mention those Modules on the file /etc/modprobe.d/blacklist.conf

------------------------------------------------------------------------------------------------------

MANIPULATING KERNAL MODULES:
-* insmod, rmmod
-* modeprobe, ismod
-* depmod

insmod   -> deps for modprobe, path only,No dependencies, fail with no explanation.
modprobe -> Frontend of insmod , Advanced, Name only, with depencdenvies, needs maps.
depmod   -> To update the database of modules dependencies.

Example usage:
#cd /lib/modules/4.15.0-42-generic/kernal/drivers/net #goto modules folder
#insmod /lib/modules/4.15.0-42-generic/kernal/drivers/net/abc.ko #It will show some errors
#modprobe abc #It just load the module successfully.
#lsmod #To check wheather it is loaded.
#rmmod abc #To unload the module previously loaded.
#lsmod #To check wheather is unloaded.
#depmod #To update the database of modules dependencies.

--------------------------------------------------------------------------------------------------------


--------------------------------------------------------------------------------------------------------

TESTING NETWORK CONNECTIVITY: (PRO TIP:Check the cable)
-* ping
-* ip add

ip add     -> check the ip address.

ip route   -> check local ip route.

ping <url> -> check is the internet is working. 

-----------------------------------------------------------------------------------------------------------

TESTING DNS:
-* ping, dig
-* nslookup, host

dig @server host
nslookup host server
host host server

-----------------------------------------------------------------------------------------------------------

LOCATING COMMON NETWORK CONFIG FILES:
-* /etc/hosts
-* /etc/resolv.conf
-* /etc/nsswitch.conf
----------------------------------------------------------------------------------------------------------
IDENTIFYING UBUNTU AND DEBIAN NETWORK FILES:
-* /etc/network/interfaces
-* /etc/netplan/*
-* NetworkManager
----------------------------------------------------------------------------------------------------------
IDENTIFYING NETWORK CONFIG FILES IN CENTOS:
-* NetworkManager 
-* /etc/sysconfig/

-----------------------------------------------------------------------------------------------------------

NETWORKING BONDING MODES:
Mode	    Discription   Need Switch Support?
 0	     balance-rr        sorta
 1	     active-backup      no
 2           balnce-xor         yes
 3	     broadcast          yes
 4           802.3ad            yes
 5           balance-tlb         no
 6           balance-alb         no

* If you have dump switch use      -> balance - alb
* switch supports link aggregation -> 802.3ad
   802.3ad is Industrial standard.

-----------------------------------------------------------------------------------------------------------

CONFIGURING NETWORK BONDS:
-* Ubuntu
-* CentOS

Ubuntu:

#Editing /etc/netplan

File Content:
#Let NetworkManager manage all devices on this system
network:
	version: 2
	renderer: networkd
	ethernets:
		eth0:
			dhcp4: false
	bonds:
		bond0:
			dhcp4:false
			interfaces:
				- eth0
			addresses: [10.10.10.10/24]
			gateway4: 10.10.10.1
			parameters:
				mode: active-backup
			nameservers:
				addresses: [8.8.8.8]


CentOS:

#Editing /etc/sysconfig/network-scripts/ifcfg-eth0

File Content:
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
NAME=eth0
DEVICE=eth0
ONBOOT=yes
MASTER=bond0
SLAVE=yes


#Editing /etc/sysconfig/network-scripts/ifcfg-bond0

File Contents:
DEVICE=bond0
NAME=bon0
BONDING_MASTER=yes
IPADDR=10.10.10.15
PREFIX=24
ONBOOT=yes
BOOTPROTO=none
BONDING_OPTS="mode=6 miimon=100"

--------------------------------------------------------------------------------------------------------


--------------------------------------------------------------------------------------------------------

UNDERSTANDING GPT AND MBR:
-* Pretective MBR
-* Differences

GPT -> GUID(Globally Unique Identifier) Partition table.
MBR -> Master Boot Record. 

	GPT 						MBR
 * Multiple copy of "Table of Content"		 Single Copy of 'Table of Content'
 * /dev/sdan                                     /dev/sdan
 * Max partitions of 1-128                       Max partitions of 1-4
 * Has disk size of real time unmeetble.         Has size of 2TB.

--------------------------------------------------------------------------------------------------------------

FILESYSTEM HIERARCHY:
-* Real/virtual
-* Relative vs Absolute
-* Network Mounts


                                                               Local /dev/sda1
                            Second Drive   ------------> root(/)  <-------------- Virtual Filesystem
	                    /dev/sdbn   /mnt/data         ^    ^		        /proc/  and /sys/
			                	          |    |	
                                                          |    |
                                                          |    |
			      USB Drive    ---------------|    |----------------- Remotefs NFS
                             /media/usb1                                                      /home/


Relative Path:
	~(tilda) -> Home dir of user
	.        -> Current Folder 
	..       -> Previous Folder

Absolute Path:
	/home/username/

--------------------------------------------------------------------------------------------------------------

CREATING PARTITIONS:
-* parted / gparted 
-* fdisk
-* info tools

Info Tools:
 $lsblk , $df ,$ fdisk -l 

Virtual partitions:
$cat /proc/partitions

---------------------------------------------------------------------------------------------------------------

FORMATTING VARIOUS PARTITIONS:

	ext4	              xfs			 btrfs(butterfs)	      dos
 * Most Common               older                         New kid                    NTFS
 * Mature                    still used by centos          Many features              vfat
 * later versions            has its own set of tools      abandoned?                 fat32
   supports journals
 * rebust (powerful)

----------------------------------------------------------------------------------------------------------------

MOUNTING PARTITIONS: (Manually and at boot)
-* mount/umount
-* /etc/fstab
-* blkid

Drives can be mounted manually to desired 'empty' folder.

lsblk -> to view filesystem type with UUID.

Mount Syntax:
	$mount -t fstype /dev/sdbn /mnt/dir
Example:
$ mount -t ext4 /dev/sdb1 /mnt/10gig

mount -> list mount points of all partitions mounted on the system.

Umount:
	$umount /mnt/data

Automount on boot:
Edit the File on /etc/fstab

$ mount -a #To mount all partitions from fstab

-----------------------------------------------------------------------------------------------------------------

SCANNING FILESYSTEMS:

-* tune2fs
-* /etc/fstab
-* fsck

 Flowchart :
       Is PASS setup in /etc/fstab
                 |
           |------------|
    (0) NO |            |Yes (1 or 2)
	   |            |
	Don't    Has MAX mounts been reached?
	Scan            |
		        |
                |---------------|
	      NO|               |Yes
		|		|		        
	     Don't Scan       Scan!

Example conf for Auto scanning disk:
#tune2fs -l /dev/sdb1 
Look over it If 'Maximum mount count' is negative value It never gone to scan on boot.
So,change it to enable scan on boot for the disk.
#tune2fs -c 10
Now the disk can scanned on boot After 10 mounts reached. 

Use 'fsck' command to scan a disk manually.

-------------------------------------------------------------------------------------------

LVM(Logical Volume Manager) IN A NUTSHELL:

-* Physical Volumes
-* Volume Groups
-* Logical Volumes


	PV				VG			LV

       10G 10G                         40G  ------------------> 7G Part from VG
       10G 10G                 - Bucket of Storage         - format with 
   - Hard drives               - No partition                filesystems
   - RAID device
   - Partition

LVM Used in CentOS for Single disk.

BUILDING AN LVM SYSTEM:

-* pvcreate
-* vgcreate
-* lvcreate

Example usage of building:
#pvcreate /dev/sdb /dev/sdc /dev/sdd /dev/sde  #To Create a Physical Volume
#pvdisplay  #To view what we done by previous command.

#vgcreate nameforvg /dev/sdb /dev/sdc /dev/sdd /dev/sde  #To create a Volume Group
#vgdisplay  #To view what we done by previous command.

#lvcreate -L 32G -n nameoflv nameofvg #To create Logical Volume
#lvdisplay  #To view what we done by previous command.

To Assign a partition type:
#mkfs.ext4 /dev/nameofvg/nameoflv

To extend the size of previously created logical volume or lv, here 5G of size:
#lvextend -L+5G /dev/nameofvg/nameoflv	


-------------------------------------------------------------------------------------------

RAID(Redundent Array Independent Disks) LEVELS:

-* RAID 0 
-* RAID 1
-* RAID 5
-* OTHERS


RAID0
* If you Lose one drive you lose all your data.
RAID1
* If you lose one drive you lose None of your data.
RAID5
* If you lose one drive you lose None of your data. One Drive losing increase
  By increaing no of drive used on the RAID.
* Disadvange of RAID5 is Your ONE drive space among the drive space.



CONFIGURING WITH mdadm:

-* Partitions vs raw
-* mdadm.conf
-* /proc/mdstat

Drive config for RAID:

* Your must a single partition on drive with a reduced size than actual 
  drive size to a overcome size missmatch on replacing the damaged drive.

Example usage of creating linux software RAID5:

Output lsblk of those Drives should be:

sdb	8:0	0	10G 	0 disk
|_sdb1  8.16    0       9.9G	0 part
sdc     8:0     0       10G     0 disk
|_sdc1  8.16    0       9.9G    0 part
sdd     8:0     0       10G     0 disk
|_sdd1  8.16    0       9.9G    0 part
sde     8:0     0       10G     0 disk
|_sde1  8.16    0       9.9G    0 part


To create RAID,Here RAID5:
#mdadm --create --verbose /dev/md0 --level=5 --raid-devices=4 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1 #To Create RAID5 for desired partitions
#cat /proc/mdstat #To view Changes made by previous command
#ls /dev | grep md #Check Device md created or not.
#mdadm --detail --scan #To view details of previously created RAID5 virtual device
#mdadm --detail --scan > /etc/mdadm/mdadm.conf #To Create RAID5 config.
#mkfs.ext4 /dev/md0 #To define partition type for the RAID5 virtual partition.

-------------------------------------------------------------------------------------------


INSTALLING TARBALLS(.tar.gz):

-* 3 step (extract,compile,install)
-* disadvantages

step 1 Extract
Example Usage:
#tar -zxvf filetoinstall.tar.gz #Extract

step 2 Compile
Example Usage:
#cd filetoinstall/
#make #Complie

step 3 Install
#sudo mv filetoinstall /usr/local/bin #Install
#filetoinstall #execute

Disadvantage:
It cant be Upgradable.

-------------------------------------------------------------------------------------------

MANAGING .deb FILES (DEBIAN BASED DISTRO):
-* apt, apt-get ,aptitude
-* dpkg

apt			aptitude			apt-get

* Newer               * Older                      * oldest
* Simpler             * still works                * still works
* Use this!           * don't use                  * don't use

dpkg :
#sudo dpkg -i abc.deb #To install .deb files  
If dependency not statisfied:
#sudo dpkg -r abc.deb


--------------------------------------------------------------------------------------------

MANAGING RPM PACKAGES:
-* Yum, Dnf, RPM
-* No 2 step required.

	Yum			Dnf			RPM
* Yellow Dog Modified     * Dandified YUM        * Low level tool
* Handles dependencies	  * In fedora,will
                            replace Yum
* Updates repo when 
  Installing/Upgrading


Example Usage:
# yum install <toolname>
# yum upgrade #to Upgrade the system

-----------------------------------------------------------------------------------------------

CONFIGURING APT REPOSITORIES:
-* sources.list / sources.list.d
-* GPG Keys
-* PPA

sources.list:
 location: /etc/apt/sources.list
	can add official repo sources on this fie sources.list
Sources.list.d:
	can add unofficial repo sources on this folder sources.list.d
GPG Keys:
 To check list of existing keys
	# apt-key list
PPA:
 Automate sourcelist adding.
 Example usage:
 # add-apt-repoitory ppa:/webup&team/atom

-----------------------------------------------------------------------------------------------

CONFIGURING YUM REPOSITORIES:
-* add repo
-* edit config

Add Repo:
 located at: /etc/yum.conf
 /etc/yum.repos.d/
 .repo files are located which has data sources of repos.

-----------------------------------------------------------------------------------------------

IDENTIFYING LESS COMMON PACKAGE MANAGERS:
-* openSUSE (zypper)
-* Arch linux (pacman)

OpenSUSE:
 zypper:
 # zypper -h #help
 # zipper in <toolname> #to install a tool

Arch linux:
 pacman: 
 # pacman -h #help
 # pacman -Syy #to upgrade the system
 # pacman -Sy <toolname> #to install a tool

-----------------------------------------------------------------------------------------------


-----------------------------------------------------------------------------------------------

MANAGING LOCAL USERS:
-* adduser
-* useradd, usermod, userdel

Local use information in the system:
 *Fullname
 *Username
 *Password
 *Primary group
 *Secondary group
 *Home directory
 *Shell program
 *Office, etc(not ready used)

Commands:
 # useradd -h #help
 # useradd -d /home/suzy -s /bin/bash/suzy
 # passwd suzy #set password for suzy
 tip: login to local user using SSH
 Example:
  # ssh suzy@localhost

Adduser:
 # adduser frank #adduser using adduser command and it will ask for Fullname, Room Number etc,.
Userdel:
 # userdel -h #help
 # useradd -r frank
Useradd:
 # usermod -h #help

-----------------------------------------------------------------------------------------------

MANAGING LOCAL GROUPS:
-* primary
-* secondary / suplementary

Commands:
-* groupadd
-* groupmod
-* groupdel

Usage:
 # groupadd -h #help
 # groupmod -h #help
 # groupdel -h #help
 # groups <username> #to list groupd of username
 # usermod -a -G public <username>

-----------------------------------------------------------------------------------------------

QUERYING USER ACCOUNTS:
-* forenics
-* sanity

Commands:
 # whoami
 # w
 # pinky
 # id <username>
 # last

-----------------------------------------------------------------------------------------------

MANAGING  GROUP & PASSWORD FILES:
-* Shadow files
-* /etc/passwd
-* /etc/group
-* vipw / vigr

                                                       |
                                     readable by all   |         Only root access
                                            ^          |
                   _________________        |          |        __________________
                   |  ____________  |       |          |       |               |  \
                   | |            | |       |          |       | user0:XdhjkdA |   \
    _              | |user0:abcdef| |       |          |       | BFHTWdfvsrt%$ |____| 
   (_)             | |123456789009| |-------|          |       | 56CC4v#$vbBFf      |
  \-|-/ ---------> | |876543217846| |                  |       | Xb4$%#*&^Ydx7      |
   _|_             | |abcdefqwerty| |                  |       | BFHTWdfvsrt%$      |
  |   |            | |____________| |                  |       | 56CC4v#$vbBFf      |
                   |________________|                  |       |____________________|
                                                       |
                                                       |       /etc/shadow
                                            
                                                  Authentication by system

Commands:
 # ls -l /etc/passwd
 # ls -l /etc/shadow
 # ls -l /etc/group
 # ls -l /etc/gshadow
 # sudo vi /etc/passwd
 # sudo vipw
 # sudo vipw -s
 # sudo vigr
 # sudo vigr -s

------------------------------------------------------------------------------------------------

USER AND GROUP QUOTAS:
-* Soft / hard limits
-* quota check / quotation

Commands:
 content of /etc/fstab
 
 /dev/sdb	/mnt/disk	ext4	defaults,userquota	0	0

 # mount
 # sudo quotacheck -au
 # ls /mnt/disk/
 # sudo quotaon -a
 # sudo wdquota <username>
 # cd /mnt/disk/
 # ls
 # dd if=/dev/zero of=file1 bs=ik count=400
 # ls -l
 # dd if=/dev/zero of=file2 bs=ik count=400
 # ls -l
 # dd if=/dev/zero of=file3 bs=ik count=400
 # ls -l

--------------------------------------------------------------------------------------------------

IDENTIFYING USES PROFILES:
-* System wide
-* individual

All Users:
	----> /etc/environment
                               _
                                |
              /etc/bash.bashrc  |   ----> One or Other
              /etc/bashrc       |
                               _|

	      /etc/profile
Individual
	----> /home/users/.bashrc
                                        _
                                         | 
              /home/users/.profile       |   ----> One or Other
	      /home/users/.bash_profile  |
                                        _|

 # cd /etc
 # ls -l | grep profile
 # vi ../profile

---------------------------------------------------------------------------------------------------


---------------------------------------------------------------------------------------------------

CHOOSING A TEXT EDITORS:
-* nano
-* vi

Surviving vi:

    _______________________                              ____________
   |                      |                             |           |
   | Save and Quit        |     Press "i" or "a"        | Typing    |
   | :wq <enter>          | --------------------------> |      Text |
   | Quit Without Saving: |                             |           |
   | :q! <enter>          |                             |           |
   | Save:                | <-------------------------- |           |
   | :w  <enter>          |     Press "Esc"             |           |
   |______________________|                             |___________|
     Command Mode                                        Insert Mode

Nano:
 ctrl+s   ----->  Save
 ctrl+x   ----->  Quit

--------------------------------------------------------------------------------------------------

USING 'HEAD', 'TAIL' 'MORE' and 'LESS' (To view text):
-* viewing
-* searching

Commands or tools:
 # cat file.txt
 # head file.txt
 # head -n 20 file.txt
 # tail file.txt
 # more file.txt
 # more file.txt
 # less file.txt   # /search something

-------------------------------------------------------------------------------------------------

SEARCHING FOR TEXT WITH 'GREP':
-* regex
-* strings

Usage:
                             _
 # grep -F String file.txt    |  ---> Same
 # cat file | grep -F string _|
            ^
            |---- Pipe Symbol

Command Usage:
 # grep dhep /var/log/syslog
 # dmeag
 # dmesg | grep -F dhep
 # grep dhep /var/log/syslog | grep init

--------------------------------------------------------------------------------------------------

STDIN, STDOUT and STDERR:
-* redirection
-* |(pipe), < and >

                                                              _______________
                    ________________  Redirect with          |_________-_-_*_|
  Redirect with    |        |       |  > (greater than)      |               |
   < (less than)   |        |       |----------------------> |_______________|
   or | (pipe)     |        |       |    (STDOUT)
 ----------------> |----------------|
                   |        |       |Redirect with 2>        _______________
                   |        |       |---------------------->|_________-_-_*_| 
                   |________|_______| (two greater than)    |               |
                                          (STDERR)          |_______________|
                                                            
Command Usage:
 # grep text file.txt
 # cat file.txt | grep text
 # grep text < file.txt
 # ls > results.txt
 # cat results.txt
 # ls ff 2> error.txt
 # cat error.txt

--------------------------------------------------------------------------------------------------

INPUT / OUTPUT, Tips and Tricks:
-* /dev/null
-* tee
-* xargs
                                                    |              STDOUT
                                                    |     App1 ----->|
 /dev/null                                          |                |    STDIN
 black hole,bit bucket                              |                |------> xargs 
                                                    |                           |                                        |
 # echo "Important Info" > /dev/null                |                           |
            (bad ideal)                             |                           |
----------------------------------------------------|                           |--> App2
                                                    |                            (Arguments)
             STDIN              STDOUT              |
  text ----------------> tee ------------->  text   |
                          |                         |
                          |                         |
                          |------> text             |
                                                    |

Commands and Usage:
 # echo "Hello"
 # echo "hello" > /dev/null
 # echo "hello" 2> /dev/null
 # ls Documents ff
 # ls Documents ff > /dev/null 2> &1
 # ls
 # cat file.txt
 # cat file.txt | tee copy.txt
 # ls
 # cat copy.txt
 # cat copy.txt | xargs mkdir
 # cat copy.txt | xargs rmdir

------------------------------------------------------------------------------------------------

TEXT MANIPULATION WITH COMMANDS LINE TOOLS:
-* cut / paste
-* sort
-* wc

Command and Usage:
 # ls
 # cat file.txt
 # sort file.txt
 # sort file.txt > sorted.txt
 # man sort
 
 wc ----> Word Count

 # wc file.txt
  4 4 27    file.txt
  | | |
  | | |----> full word count
  | |------> lines
  |--------> words

 # cat file.txt
 # cut -c 1 file.txt
 # cut -c 1 file1.txt
 # cut -c 3,4,5 file1.txt
 # paste file.txt file1.txt
 # paste file.txt file1.txt > joined.txt

-------------------------------------------------------------------------------------------------

EDITING TEXT WITH AWK, SED :
-* Stream Editor
-* Aho, wein berger, kernighan

Command and Usage:
 # cat file1.txt | sed s/monkey/dolphin/g
 # awk '{print $1}' joined.txt
 # awk '{print $2 $1}' joined.txt
 # awk '{print $2 " " $1}' joined.txt

--------------------------------------------------------------------------------------------------

HARD LINKS AND SOFT(symbolic) LINKS:
-* inodes
-* file references

Command and usage:
 # ls -l
 # ln -s file.doc mylink.doc
 # ls -l
 # mv file.doc file1.doc
 # ls -l 
 # ln myfile.doc mylink.doc
 # ls -l
 # mv my_file.doc coolfile.doc
 # ls -li
 # find ~ -samefile myfile.doc

--------------------------------------------------------------------------------------------------

'FIND' AND 'LOCATE' :
-* find vs locate
-* pros / cons

Command and Usage:
 # locate oldfile
 # locate file
 # locate fileon
 # sudo updatedb
 # locate fileon
 # find / -name *file*
 # find . -name *file*

-------------------------------------------------------------------------------------------------

COPYING FILES OVER THE NETWORK:
-* ssh / scp
-* rsync

Command and Usage:
 # ssh centos
 # ls Desktop/
 # scp user@centos:/home/user1/Deaktop/cool_pic.jpg
 # rsync -av centos:/home/user1/Desktop .

-------------------------------------------------------------------------------------------------

MANAGING SERVICES WITH 'SYSTEMCTL':
-* enable / disable
-* start / stop

Command and Usage:
 # systemctl status httpd
 # systemctl enable httpd
 # systemctl start httpd
 # systemctl disable httpd
 # systemctl status httpd

------------------------------------------------------------------------------------------------

MAINTAINING 'SYSV' RUNLEVELS:
-* Various Modes
-* Switching Modes
-* Setting Defaults

                         Sysv
 Debian/Ubuntu                         CentOS/SUSE
 0 - Halt                             0 - Halt 
 1 - Single User Mode                 1 - Single User Mode
 2 - Full, Multiuser, GUI if          2 - Multi user, no net
     Installed.
 3-5 - Nothing                        3 - Multiuser, with net
 6 - Reboot                           4 - Not set
                                      5 - Multi user GUI
				      6 - Reboot

Command and Usage:
 # runlevel
 # telinit 3
 # telinit 5
 # runlevel
 # vi /etc/inittab
 # telinit 6

--------------------------------------------------------------------------------------------------


MAINTAINING SYSTEMD INIT:
-* Various Modes
-* Switching Modes
-* Setting Defaults

  Runlevel                  Boot target
     0                       PowerOff
     1                       Rescue
     3                       Multi user
     5                       Graphical
     6                       Reboot

Command and Usage:
 # systemctl get default
 # systemctl set default multi-user-target
 # systemctl get default
 # systemctl isolate multi-user

---------------------------------------------------------------------------------------------------

MANAGING SERVIVES WITH 'SYSV':
-* /etc/init.d/
-* service
-* chkconfig

Command and Usage:
 # ls /etc/init.d/
 # service sshd start
 # service sshd status
 # chkconfig --list sshd
 # chkconfig sshd on
 # chkconfig --list sshd
 # chkconfig --level 3 sshd on
 # chkconfig --list sshd


---------------------------------------------------------------------------------------------------

MANAGING SERVICES WITH SYSTEMD:
-* systemctl
-* enable / disable
-* start / stop / status

Command and Usage:
 # ls /etc/systemd/system
 # locate sshd.service
 # systemctl sshd.service
 # systemctl status sshd
 # systemctl disable sshd
 # systemctl status sshd
 # systemctl stop sshd

----------------------------------------------------------------------------------------------------


----------------------------------------------------------------------------------------------------

UNDERSTANING NETWORK SERVER ROLES:
-* Local services 
-* Centralized Services
-* Planning


            Centralized			                  Individual
 

          ___________________________________________________
         |                  |       |                        |
         | DHCP             |       |                   SSH  |
         |                  |       |                        |
         | DNS              |->NTP<-|            Config      |
         |                  |       |               Client   |
         |   Config         |       |                        |
         |    Management    |       |    Docker              |
         |__________________|_______|________________________|

              Server or Services ?
                             
           _____                ________               _________
          | DNS |              |  DNS   |             |  _____  |
          |_____|              |        |             | | DNS | |
                               |        |             | |_____| |
           ______              |        |             |  ______ |
          | DHCP |             |  DHCP  |             | | DHCP ||
          |______|             |        |             | |______||
                               |        |             |  _____  |
           _____               |        |             | | NTP | |
          | NTP |              |  NTP   |             | |_____| |
          |_____|              |        |             |  _____  |
                               |        |             | | WEB | |
           _____               |        |             | |_____| |
          | WEB |              |  WEB   |             |_________|
          |_____|              |________|
          
         Expansive               Messy                  Spamual
                                                       Potential

Understanding web servers and ssl:
-* Processes
-* Certificate Authority

-------------------------------------------------------------------------------------------------

UNDERSTANDING LOCAL NETWORK SERVER ROLES:
-* file
-* print
-* mail
-* proxy

Service  |     Local                 |  Cloud
----------------------------------------------------
file     | samba -> Windows          |  Dropbox
         | NFS   -> Linux, Mac, Win  |  OneDrive
         | Netatalk -> Apple         |  GoogleDrive
---------|---------------------------|--------------
print    | caps -> win, Mac, Linux   |  GooglePrint
         | Netatalk -> Apple         |  Air Print
---------|---------------------------|---------------
mail     | Postfix                   |  Gmail
         | EXim                      |  Outlook
         | Sendmail                  |  Yahoo
---------|---------------------------|---------------
proxy    | Squid                     | *OpenDNS
         | Squidgame                 | AKomai
---------|---------------------------|---------------

--------------------------------------------------------------------------------------------------

UNDERSTANDING AUTHENTICATION AND DATABASE SERVER ROLES:
-* purpose
-* importance

---------------------------------------------------------------------------------------------------

UNDERSTANDING CENTRALIZED LOGGING AND MONITORING:
-* syslog
-* SNMP

SNMP  ---> Simple Network Management Protocol.

  _____________                             ----------   Mainly used for pulling
 |_______-_+_x_| <--------|----------------| Internet |    data.
 |             |          |   SNMP          ---------- 
 |_____________|          |------------> Interface Statistics.

 --------------------------------------------------------------------------------------------------

UNDERSTANDING VPNs:
-* concept
-* options

Concept:
  A virtual private network, or VPN is a set of technologies which are used to link 
computers to create a private network. Another network is used to carry the data, 
which is encrypted. The carrier network will see the packets of data which it
routes. To the users of the VPN, it will look like the computers were directly
connected to each other.
The VPN model can guarantee the following:
 * Confidentiality: The carrier network will route the data, but it will be unable to
   decrypt it.
 * Sender authentication: People need to authenticate themselves, to be able to
   use the network.
 * Message integrity: Messages transported across the network cannot be changed 
   easily while they are in transport. When a message was changed, it is possible
   to detect this.
 Example scenario:
    In a business context, VPNs are often used to connect different office locations,
    or to allow people working from outside the company network to access its resources. 
    People can use their computer to connect to their work network and see work websites 
    that cannot be seen on the normal internet. 

Options:
 * OpenVPN
 * SSH
 * LLTP
 * IPsec

 --------------------------------------------------------------------------------------------------

UNDERSTANDING CONTAINERS:
-* Different than VM?
-* Options (LxC, Docker, Kubemotes)

Concepts:
REF:https://www.ibm.com/cloud/blog/containers-vs-vms

Command and Usage:
 # docker run -it ubuntu /bin/bash
 # del if=/dev/zero of=/dev/null  

---------------------------------------------------------------------------------------------------

UNDERSTANDING CLUSTERING AND LOAD BALANCING:
-* Is a hotdog a sandwith?

---------------------------------------------------------------------------------------------------

SYSTEM-WIDE CRON JOBS:
-* Pre-made folders
-* Manual Schedules

                            Cron Fields
Minute      Hour      Day of Month      Month       Day of week
  *          *             *              *              *
  */5        3             *              *              *
  2          4             13             7              2
  0          6             *              *              1-5

Command and Usage: 
 # cd /etc/cron.d
 # ls
 # vi sysstat
 # cd ..
 # ls | grep cron
 # cd cron.daily/
 # vi logrotate
 # vi /etc/crontab

 --------------------------------------------------------------------------------------------------

 PERSONAL CRONTABS & THE AT DAEMON:
 -* crontab -e
 -* at

 Command and Usage:
 # crontab -e
 # cat ~/timetracker.log
 # ls -l
 # at now +1 minute
  at> echo "this was a one off" >> /home/bob/timetracker.log
  at>
  ctrl + d
 # atq
 # cat ~/timetracker.log
 # at now +1 minute  
  at> echo "hello" >> /home/bob/timetracker.log
  at>
  ctrl + d 
 # atq 
 # at tomorrow
  at> echo "test tomorrow" >> /home/bob/timetracker.log
  at>
  ctrl + d
 # atrm 6
 # atq
 # cat timetracker.log

---------------------------------------------------------------------------------------------------
HANDLING FOREGROUND AND BACKGROUND PROCESSES:
-* fg, bg, jobs
-* ctrl + z
-* nohup

Command and Usage:
 # sleep 1
 # sleep 111111 &  #the & means before mentioned is background process
 # sleep 22222 &
 # jobs
 # fg 2
 # jobs
 # sleep 333333
 # jobs
 # bg 2
 # jobs
 # fg 1
 # fg 2
 # jobs
 # clear

 # myhello  #Which prints Hello! 2 Seconds once
 # myhello & #Now its background Process

 ##LOGOUT and LOGIN
 # ps aux | grep myhello
 # nohup myhello &
 # nohup: ignoring input and appending output to 'nohup.out'
 # jobs

 ##LOGOUT and LOGIN
 # ps aux | grep myhello
 # ls
 # cat nohup.out

---------------------------------------------------------------------------------------------------

FINDING LOCAL DEVICES:
-* lsusb, pci, dev, blk, cpu 
-* dmesg

 # dmesg
 # ls#PRESS 'Tab' to list command start with ls
 # lsblk
 # lscpu
 # lsdev
 # lspci
 # lsmem

---------------------------------------------------------------------------------------------------

UNDERSTANDING VIRTUAL FILESYSTEMS:

-* /proc/
-* /sys/
-* /dev/

/proc ---> All system Processes.
/sys  ---> All system running files.
/dev  ---> All system devices.

---------------------------------------------------------------------------------------------------

CUPS (Printing and Command Line Tools):
-* Web interface
-* lpr, lpq, lprm

Command and Usage:
 # lp#PRESS 'Tab' to list command start with lp
 # lpq 
 # echo "This is a test" | lpr
 # cat my_documents.txt 
 # lpr my_documents.txt 
 # lpq
 # lprm 10

---------------------------------------------------------------------------------------------------

UNDERSTANDING 'UDEV':
-* sysfs
-* rules 

Command and Usage:
 # udevadm info /dev/sr0
 # ls /dev | grep sr0
 Example scenario:
 # /etc/udev/rules.d/ && ls
 # vi 10-snap.core.rules
 Content of file: 10-snap.core.rules
 KERNAL=="sr0", SUBSYSTEM=="block", SYMLINK="my_dvd"
 # udevadm trigger
 # cd /dev 
 # ls
 # ls -l | grep dvd 

---------------------------------------------------------------------------------------------------