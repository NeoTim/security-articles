* shared memory (and it's acl's)

  Fedora 19 Desktop does *not* suffer from the usage of world writeable shared
  memory.

  ::

     $  ipcs  # on Fedora 19 Cinnamon Desktop

     ------ Message Queues --------
     key        msqid      owner      perms      used-bytes   messages

     ------ Shared Memory Segments --------
     key        shmid      owner      perms      bytes      nattch     status
     0x00000000 1376256    dsk        600        393216     2          dest
     0x00000000 262145     dsk        600        4194304    2          dest
     0x00000000 1703938    dsk        600        393216     2          dest
     0x00000000 1507331    dsk        600        524288     2          dest
     0x00000000 2490372    dsk        600        393216     2          dest
     0x00000000 1835013    dsk        600        393216     2          dest
     0x00000000 2621447    dsk        600        524288     2          dest
     0x00000000 3440648    dsk        600        393216     2          dest
     0x00000000 8159241    dsk        600        4194304    2          dest
     0x00000000 3571722    dsk        600        393216     2          dest
     0x00000000 3604491    dsk        600        524288     2          dest
     0x00000000 12877836   dsk        600        393216     2          dest
     0x00000000 7110669    dsk        600        393216     2          dest
     0x00000000 13041679   dsk        600        1048576    2          dest

     ------ Semaphore Arrays --------
     key        semid      owner      perms      nsems
     0xcbc384f8 0          dsk        600        1

  Xfce Desktop is also OK.

  TODO: Scan other Desktop environments (GNOME, KDE).

* World writable files and directories.


  Problems exist on Fedora 19 Desktop! ::

    $ sudo find / -perm -0666 -type f | grep -v proc | grep -v home
    /sys/fs/selinux/member
    /sys/fs/selinux/user
    /sys/fs/selinux/relabel
    /sys/fs/selinux/create
    /sys/fs/selinux/access
    /sys/fs/selinux/context
    /usr/share/pgadmin3/docs/en_US/pgadmin3.hhp.cached

  https://bugzilla.redhat.com/show_bug.cgi?id=983838

  TODO: scan *all* packages using checksec-ng for such problems.

  TODO: what about ``sudo find / -perm -0002 -type f ...``

* Enumerate suid files

  ::

      $ sudo find / -perm /2000 -o -perm /4000 -exec du -hs {} \;
      32K     /usr/bin/su
      80K     /usr/bin/gpasswd
      44K     /usr/bin/mount
      24K     /usr/bin/chsh
      28K     /usr/bin/passwd
      56K     /usr/bin/crontab
      32K     /usr/bin/fusermount
      60K     /usr/bin/ksu
      40K     /usr/bin/newgrp
      124K    /usr/bin/sudo
      24K     /usr/bin/chfn
      32K     /usr/bin/fusermount-glusterfs
      32K     /usr/bin/umount
      164K    /usr/bin/staprun
      56K     /usr/bin/at
      64K     /usr/bin/chage
      24K     /usr/bin/pkexec
      2.2M    /usr/bin/Xorg
      36K     /usr/sbin/unix_chkpwd
      40K     /usr/sbin/userhelper
      12K     /usr/sbin/usernetctl
      112K    /usr/sbin/mount.nfs
      12K     /usr/sbin/pam_timestamp_check
      16K     /usr/lib/polkit-1/polkit-agent-helper-1
      304K    /usr/lib64/dbus-1/dbus-daemon-launch-helper
      16K     /usr/libexec/qemu-bridge-helper
      16K     /usr/libexec/spice-gtk-x86_64/spice-client-glib-usb-acl-helper


  Fedora 19's kpp is neither bloated nor suid.

  TODO: verify against Fedora 19 "whitelist" (talk with bressers and esm)

  TODO: scan all packgaes for bloated suid binaries (self size + size of dependencies)

* TODO: udp/tcp/unix sockets exposed locally (netstat –pln)

* TODO: Auditing DBUS (talk to fweimer)

* Play^W^W Audit various setgid games

  - gnomes-mines is *no* longer setgid
  - look into db parsing code and do some fuzzing (talk to Ramon and Huzaifa).

  TODO: make a list of all games which are setgid (or privileged).

* Audit various X clients (talk to fweimer)

* Look into QT_PLUGIN_PATH and GTK_MODULES

* Audit Xlib (talk to fweimer)

* Enhance checksec-ng to detect calls to "getspnam()"

  - Can we fix getspnam to avoid spamming memory with sensitive data? (XX:
    dkholia is taking a look)

  - Memory snooping attacks (from user-space) don't work since the kernel
    scrubs (zeros) the memory pages before handing them out again.

  - Cold Boot attacks will however work just fine.



