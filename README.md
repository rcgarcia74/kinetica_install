# kinetica_install

Switch to root user
```
sudo su 
```
Make the install directory
```
mkdir /opt/installer
```

Change to the new directory
```
cd /opt/installer/
```

Install wget
```
yum install wget
```

Grab the Kinetica installer
```
wget http://repo.kinetica.com/yum/6.1.0/CentOS/6/x86_64/gpudb-cuda80-license-6.1.0.4.20180209223113.ga-0.el6.x86_64.rpm
```


Grab the Kinetica Repo
```
wget -O /etc/yum.repos.d/kinetica-6.1.0.repo http://repo.kinetica.com/yum/6.1.0/CentOS/6/x86_64/kinetica-6.1.0.repo
```

Install PCI Utils
```
yum -y install pciutils
```

Disable Noveau
```
cat <<EOF | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
 blacklist nouveau
 options nouveau modeset=0
 EOF
```

Backup Grub Conf file
```
cp /boot/grub/grub.conf /boot/grub/grub.conf.bak
```

Edit Grub Conf file and add the line 
```
elevator=noop
```

Move initramfs
```
mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r)-nouveau.img
```

Build initramfs image
```
dracut /boot/initramfs-$(uname -r).img $(uname -r)
```

Install EPEL release 7
```
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
yum install epel-release-7-11.noarch.rpm
```
Install Kernel dependencies
```
yum -y install kernel-devel kernel-headers gcc dkms acpid
```

Upgrade kernel
```
yum upgrade kernel
```

Reboot vm
```
sudo reboot now
```

Change directory
```
cd /opt/installer/
```

Install CUDA
```
wget https://developer.nvidia.com/compute/cuda/9.1/Prod/local_installers/cuda-repo-rhel7-9-1-local-9.1.85-1.x86_64
rpm -i cuda-repo-rhel7-9-1-local-9.1.85-1.x86_64
yum clean all
yum install cuda
```

Check if NVidia Driver was Installed Properly
```
nvidia-smi
```

Enable NVidia Persistence
```
nvidia-smi -pm 1
```

Update OS
```
 yum update
```
 
Install Kinetica
```
yum install gpudb-cuda80-license
```

