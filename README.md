# kinetica_install For RHEL 7 in AWS
This uses the latest Kinetica 6.1.0.8.

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
yum -y install wget
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

Edit grub.conf file
```
vi /boot/grub/grub.conf
```

Edit Grub Conf file and add the line at the kernel line
```
elevator=noop
```

It should look something similar below.
```
default=0
timeout=0


title Red Hat Enterprise Linux 7 (3.10.0-693.11.6.el7.x86_64)
        root (hd0)
        kernel /boot/vmlinuz-3.10.0-693.11.6.el7.x86_64 ro root=UUID=3e11801e-5277-4d87-be4c-0a9a61fbc3da console=hvc0 LANG=en_US.UTF-8 elevator=noop
        initrd /boot/initramfs-3.10.0-693.11.6.el7.x86_64.img
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
yum -y install epel-release-7-11.noarch.rpm
```
Install Kernel dependencies

For CentOS ONLY
```
yum -y install kernel-devel-$(uname -r) kernel-headers-$(uname -r) acpid dkms
```
For RHEL ONLY
```
yum -y install kernel-devel kernel-headers gcc dkms acpid 
```

Upgrade kernel
```
yum -y upgrade kernel
```

Reboot vm
```
sudo reboot now
```

Log back in to your vm and be root
```
ssh -i your_pem_file ec2-user@your_pub_ip
sudo su
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
yum -y install cuda
```

Check if NVidia Driver was Installed Properly
```
nvidia-smi
```

Enable NVidia Persistence. Make sure to also put this into /etc/rc.local file.
```
nvidia-smi -pm 1
```

Update OS
```
yum -y update
```
 
Install Kinetica
```
yum -y install gpudb-cuda80-license
```

After the installation, all Kinetica services will be up and running. Go to your browser and enter your URL - http://your_public_ip:8080.

![alt text](https://github.com/rcgarcia74/kinetica_install/blob/master/license_window.png)

You will then be presented the Setup Wizard. Provide the private IP address of each hosts and their respective # of gpus. If you are only using one instance, then leave the Host IP to 127.0.0.1. Otherwise, use the private IPs. 


