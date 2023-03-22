# OP-TEE Release Binaries

# Description
The git contains OP-TEE binaries running based on QEMU/KVM.

## Install QEMU in Ubuntu

### Check virtualization enabled in Ubuntu
	$ egrep -c '(vmx)' /proc/cpuinfo
  If Virtualization is supported, the output should be greater than 0.
  
	$ kvm-ok
  Check if KVM virtualization is supported.
  If the kvm-ok utility is missing, install the cpu-checker package.

### Install QEMU in Ubuntu
	$ sudo apt update
	$ sudo apt install qemu-kvm
	$ sudo usermod -aG kvm $USER
  
## Run OP-TEE binaries
### Firstly, on Ubuntu host side, insert OP-TEE driver modules manually:

	$ sudo insmod tee.ko
	$ sudo insmod optee.ko

### Secondly, start OP-TEE VM based on QEMU:

Copy tee-pager_v2.bin to your $HOME folder.

For non-TDX:

	$ sudo qemu-system-x86_64 -enable-kvm -nographic -m 32M -kernel ~/tee-pager_v2.bin -serial mon:stdio -device vhost-vsock-pci,id=vhost-vsock-pci0,guest-cid=6

For TDX:

	$ sudo /usr/libexec/qemu-kvm -enable-kvm -object tdx-guest,id=tdx -machine q35,kvm-type=tdx,pic=no,kernel_irqchip=split,confidential-guest-support=tdx -no-hpet -cpu host,host-phys-bits,-kvm-steal-time -device loader,file=/usr/share/qemu/OVMF.fd,id=fd0 -nographic -vga none -m 128M -kernel ~/tee-pager_v2.bin -serial mon:stdio -device vhost-vsock-pci,id=vhost-vsock-pci0,guest-cid=6
  
