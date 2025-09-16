# Reset root password

Make these changes:

1. Start your Rocky Linux VM.
2. At the GRUB screen, press e to edit the default boot entry.
3. Find the line starting with linux or linux16.
4. Replace ro with rw
5. Add init=/bin/bash at the end of that line, like so:

   `linux /vmlinuz-... root=UUID=... rw quiet splash init=/bin/bash`
6. Boot with the modified line by pressing Ctrl + X or F10 to boot.
7. passwd root
8. sync
9. SELinux is enabled, you may need to relabel the filesystem with touch /.autorelabel before rebooting.
10. exec /sbin/init