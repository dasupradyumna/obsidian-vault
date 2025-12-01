Sometimes, new Linux kernels are rolled out for Ubuntu that can break the UX. When that happens, the simplest fix would be to use the older working kernel until an official fix is pushed to APT repository.
This can be editing `/etc/default/grub` file to modify GRUB boot-up sequence.
## Fix 1: Launch the desired Linux kernel
```bash
# List all installed Linux kernels on the device
# Output contains list - 'Ubuntu, with Linux xxxxxxxxxx'
sudo grep "menuentry 'Ubuntu," /boot/grub/grub.cfg
# Get the active Linux kernel (current session)
# For example, 6.14.0-35-generic
uname -r
# Modify GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux x.xx.x-xx-generic"
sudo nvim /etc/default/grub
sudo update-grub
# Reboot to see changes
```
## Fix 2: Launch last selected kernel
```bash
# Open grub file
sudo nvim /etc/default/grub
# Add below lines
# - GRUB_DEFAULT=saved
# - GRUB_SAVEDEFAULT=true
sudo update-grub
# Reboot to see changes
```