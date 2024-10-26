# Prerequisites

First off, you'll need:

The Zig compiler version 0.13.0
GRUB as our bootloader to boot the kernel

# Build

Now that our kernel code is done, we'll now build our kernel by running the command below:

```bash
$ zig build
```

# Verifying Multiboot

If the header isn't valid, GRUB will give an error that it can't find a Multiboot header when you try to boot it. This code fragment will help you diagnose such cases:

```bash
grub-file --is-x86-multiboot zig-out/bin/kernel.elf
```

grub-file is quiet but will exit 0 (successfully) if it is a valid multiboot kernel and exit 1 (unsuccessfully) otherwise. You can type echo \$? in your shell immediately afterwards to see the exit status.

# Booting the Kernel

You can easily create a bootable CD-ROM image containing the GRUB bootloader and your kernel using the program grub-mkrescue. You may need to install the GRUB utility programs and the program xorriso (version 0.5.6 or higher). First you should create a file called grub.cfg containing the contents:

```bash
menuentry "Zig Bare Bones" {
multiboot /boot/kernel.elf
}
```


Note that the braces must be placed as shown here. You can now create a bootable image of your operating system by typing these commands:

```bash
mkdir -p isodir/boot/grub
cp zig-out/bin/kernel.elf isodir/boot/kernel.elf
cp grub.cfg isodir/boot/grub/grub.cfg
grub-mkrescue -o kernel.iso isodir
```


Warning: GNU GRUB, the bootloader used by grub-mkrescue, is licensed under the GNU General Public License. Your iso file contains copyrighted material under that license and redistributing it in violation of the GPL constitutes copyright infringement. The GPL requires you publish the source code corresponding to the bootloader. You need to get the exact source package corresponding to the GRUB package you have installed from your distribution, at the time grub-mkrescue is invoked (as distro packages are occasionally updated). You then need to publish that source code along with your ISO to satisfy the GPL. Alternative, you can build GRUB from source code yourself. Clone the latest GRUB git from savannah (do not use their last release from 2012, it's severely out of date). Run autogen.sh, ./configure and make dist. That makes a GRUB tarball. Extract it somewhere, then build GRUB from it, and install it in a isolated prefix. Add that to your PATH and ensure its grub-mkrescue program is used to produce your iso. Then publish the GRUB tarball of your own making along with your OS release. You're not required to publish the source code of your OS at all, only the code for the bootloader that's inside the iso.

# Testing your operating system (QEMU)

In this tutorial, we will be using QEMU. You can also use other virtual machines if you please. Simply adding the ISO to the CD drive of an empty virtual machine will do the trick.

# Install QEMU from your repositories, and then use the following command to start your new operating system.

```bash
qemu-system-i386 -cdrom kernel.iso
```


Additionally, QEMU supports booting multiboot kernels directly without bootable medium:

```bash
qemu-system-i386 -kernel kernel.elf
```

# QA

qemu-system-i386 -cdrom kernel.iso 引导不了问题
安装 sudo apt-get install grub-pc-bin
<https://github.com/microsoft/WSL/issues/1043>
zig build 不能用release
