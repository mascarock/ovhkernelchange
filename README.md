# Change OVH Kernel


Step 1: Ensure your server has the GRUB-2 boot loader.

Most newer servers from OVH do, as do all VPS/Cloud installations. You can have a quick look to see if the file /etc/default/grub exists, or whether this command give a valid output: Code:

update-grub -v
grub-mkconfig (GRUB) 1.99~rc1-13ubuntu3
Step 2: Install a kernel

First make sure you have the most recent data from the repositories: Code:

apt-get update
And check what kernels are available to you with: Code:

apt-cache search linux-image
An example output: Code:

apt-cache search linux-image
linux-image-2.6.38-8-generic - Linux kernel image for version 2.6.38 on x86/x86_64 linux-image-2.6.38-8-generic-pae - Linux kernel image for version 2.6.38 on x86 linux-image-2.6.38-8-virtual - Linux kernel image for version 2.6.38 on x86/x86_64 linux-image-generic - Generic Linux kernel image linux-image-generic-pae - Generic Linux kernel image linux-image-server - Linux kernel image on Server Equipment. linux-image-virtual - Linux kernel image for virtual machines linux-image-2.6.32-25-generic-pae - Linux kernel image for version 2.6.32 on x86 linux-image-2.6.32-31-generic-pae - Linux kernel image for version 2.6.32 on x86 linux-image-2.6.32-29-generic-pae - Linux kernel image for version 2.6.32 on x86

The kernels that do not have a specific version, such as linux-image-generic, are meta packages that automatically select the most recent kernel.

The "generic" kernels are best suitable for desktop systems, "servers" for, well, servers, and so on.

Once you have decided on a kernel, install it with:

Code: Always go for the latest kernel !!

apt-get install linux-image-[your selection]
Step 3: Load a kernel

Debian and Ubuntu should already have issued a update-grub command when it installed the kernel you've selected in step

But it will not yet be loaded, reason being is that you don't see the "boot screen" of the server (except in vKVM mode or the ipKVM option) and so can't make a selection during boot time.
First determine the order the kernels are listed by GRUB, which can easily be done with fgrep or cat | grep:

Code:

fgrep menuentry /boot/grub/grub.cfg
menuentry "Ubuntu 10.10, OVH kernel 2.6.38.2-xxxx-grs-ipv6-64" { menuentry 'Ubuntu, with Linux 2.6.38-8-server' --class ubuntu --class gnu-linux --class gnu --class os { menuentry 'Ubuntu, with Linux 2.6.38-8-server (recovery mode)' --class ubuntu --class gnu-linux --class gnu --class os {

The text between the quotes will give you an idea what's-what. Do keep in mind you need to start counting from zero, so the first entry is 0, the second is 1, etc.

Now you need to edit the file /etc/default/grub and change the the GRUB_DEFAULT=0 entry according to which boot option/kernel you wish to use.

So using the previous sample output, we want to load "Ubuntu, with Linux 2.6.38-8-server", and therefore need to change the zero (0) to a one (1) in /etc/default/grub: Code:

GRUB_DEFAULT=1
Now re-update the GRUB configuration (and this is important) with: Code:

update-grub
Now reboot your server and cross your fingers. Once the server is back up, you can verify the correct kernel has loaded with uname, ie:
