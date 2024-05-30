After installing Ubuntu 20.04 on my server, my PC does not automatically boot into the system. Instead, it boots into a grub shell. 

Some commands that help me to boot from the grub shell are as follows.

## `ls` command
> Command: ls [arg …]
> List devices or files.
> With no arguments, print all devices known to GRUB.

> If the argument is a device name enclosed in parentheses (see [Device syntax](https://www.gnu.org/software/grub/manual/grub/html_node/Device-syntax.html#Device-syntax)), then print the name of the filesystem of that device.

> If the argument is a directory given as an absolute file name (see [File name syntax](https://www.gnu.org/software/grub/manual/grub/html_node/File-name-syntax.html#File-name-syntax)), then list the contents of that directory.

In my case, it should be
```
grub> ls
(proc) (memdisk) (hd0) (hd0,msdos5) (hd0,msdos1)
```

```
grub> ls  (hd0,msdos5)
Partition hd0,msdos5: Filesystem type ext* ......
```

## `set` command
> Command: set [envvar=value]
> Set the environment variable envvar to value. If invoked with no arguments, print all environment variables with their values. For the list of environment variables currently used by GRUB itself see the relevant section see [Environment](https://www.gnu.org/software/grub/manual/grub/html_node/Environment.html#Environment).

In my case, as my system is installed in (hd0,msdos5), it should be
```
grub> set root=(hd0,msdos5)
```

## `linux` command
> Command: linux file …
> Load a Linux kernel image from file. The rest of the line is passed verbatim as the kernel command-line. Any initrd must be reloaded after using this command (see [initrd](https://www.gnu.org/software/grub/manual/grub/html_node/initrd.html#initrd)).
> On x86 systems, the kernel will be booted using the 32-bit boot protocol. Note that this means that the ‘vga=’ boot option will not work; if you want to set a special video mode, you will need to use GRUB commands such as ‘set gfxpayload=1024x768’ or ‘set gfxpayload=keep’ (to keep the same mode as used in GRUB) instead. GRUB can automatically detect some uses of ‘vga=’ and translate them to appropriate settings of ‘gfxpayload’. The linux16 command (see [linux16](https://www.gnu.org/software/grub/manual/grub/html_node/linux16.html#linux16)) avoids this restriction.

In my case, it should be
```
grub> linux /boot/vmlinuz-5.15.0-67-genericr root=/dev/nvme0n1p5
```

Note that (1) After typing `vmlinuz`, you can press `tab` to auto-complete the name, and (2) `/dev/nvme0n1p5' should be the device where the root directory is on. To find this device, you can use the following command:
```
grub> cat /etc/fstab
...
# / was on /dev/nvme0n1p5 during installation
...
```

## `initrd` command
>Command: initrd file [file …]
> Load, in order, all initrds for a Linux kernel image, and set the appropriate parameters in the Linux setup area in memory. This may only be used after the linux command (see [linux](https://www.gnu.org/software/grub/manual/grub/html_node/linux.html#linux)) has been run. See [GNU/Linux](https://www.gnu.org/software/grub/manual/grub/html_node/GNU_002fLinux.html#GNU_002fLinux) for more info on booting GNU/Linux. For more information on initrds see the GNU/Linux kernel [documentation](https://docs.kernel.org/filesystems/ramfs-rootfs-initramfs.html).
> A new-style initrd (for kernels newer than 2.6) containing one file with leading path components can also be generated at run time. This can be done by prefixing an argument with newc: followed by the path of the file in the new initrd, a :, and then the GRUB file path to the file data to be be included.

In my case, it should be
```
grub> initrd /boot/initrd.img-5.15.0-67-generic
```

## `boot` command
> Command: boot
> Boot the OS or chain-loader which has been loaded. Only necessary if running the fully interactive command-line (it is implicit at the end of a menu entry).

In my case, it should be
```
grub> boot
```


Putting all these together, the commands that boot the system from the grub shell would be
```
grub> set root=(hd0,msdos5)
grub> linux /boot/vmlinuz-5.15.0-67-genericr root=/dev/nvme0n1p5
grub> initrd /boot/initrd.img-5.15.0-67-generic
grub> boot
```


