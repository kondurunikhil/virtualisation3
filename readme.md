
### CMPE283 : Virtualization
### Assignment 3: Instrumentation via hypercall

#### Team :
* Nikhil Konduru - 015998957  
* Anesha Sarguna Sekara Pandian - 016044535  

#### Contribution :  
* Nikhil -  
1. Created VM in GCP and configured.  
2. Built Linux Kernel.
3. Install KVM.
4. Wrote the test program and the shell script to execute cpuid command for the exit codes.
5. Made necessary changes to cpuid.c
* Anesha -  
1. Built Linux Kernel.
2. Installed KVM inside the Ubuntu VM.
3. Debugged fixed the errors which occurred while running the code.
4. Made changes to vmx.c

#### Steps : 
1. We used the existing GCP VM enviornment from last assignment, an Ubuntu 22.04 LTS x86/64. We enabled nested virtualization and Min CPU platform as Intel Cascade Lake. 

```
--enable-nested-virtualization --min-cpu-platform="Intel Cascade Lake"
```

2. We generated private and public SSH keys using 
```
ssh-keygen -t rsa -f ~/.ssh/google_cloud_key -C $USER 
cat /home/$USER/.ssh/google_cloud_key.pub 
```
3. Then, in the GCP VM settings, we copied the generated key to the SSH keys into the Metadata of the VM.  

4. Went to https://github.com/torvalds/linux, forked the repo to my account <br />
5. Clone it to VM  <br />
```
 git clone https://github.com/kondurunikhil/linux.git
  cd linux
  git status
 git remote -v 
```

![git remote -v](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/git_remote_v.png)

```
cp /boot/config-5.15.0-1025-gcp ~/linux/.config
```
6. Then we installed gcc, make and flex.
```
sudo apt-get install gcc
sudo apt-get install make
sudo apt-get install flex
```
7. We ran the 
```
cat /proc/cpuinfo
```
8. Installed necessary Tools <br />

```
sudo apt-get install build-essential
sudo apt-get install kernel-package
sudo apt-get install ccache 
sudo apt-get install flex
sudo apt-get install bison
sudo apt-get install libssl-dev
sudo apt-get install libelf-dev
```

to confirm that all the configurations were as needed.  

9. prepared the linux using make prepare command: <br />
```
make prepare
```
10. Build all the modules that goes into the linux kernel:<br />
```
make -j 8 modules
```
11. build the linux kernel itself:  <br />
```
make -j 8
```
12. After the modules and kernel is built, we can package them into a format that suitable for booting inside our VM <br />
```
sudo make -j 8 INSTALL_MOD_STRIP=1 modules_install
```
13. Installed the linux kernel 

```
sudo make -j 8 install
```

14. Based on the requirements, we modified ~/linux/arch/x86/kvm/vmx/vmx.c  : <br />


15. Based on the requirements, we modified ~/linux/arch/x86/kvm/cpuid.c :


16. Build and install the modules again, then install the kernel  <br />

```
     make -j 8 modules
     sudo make -j 8 INSTALL_MOD_STRIP=1 modules_install
     sudo make -j 4 install 
```

![install]()

17. As there are no errors, we can create a inner VM inside our current VM,
but first we need to install some extra tools for KVM 


```
sudo apt-get install cpu-checker
sudo apt update
sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
sudo kvm-ok
sudo reboot
```


18. Now we need to authorize a vm users for the inner VM, and verify our VM functionalities:

```
  uname -a
```

![install](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/uname-1.png)

```
  sudo adduser nikhil_konduru libvirt && sudo adduser nikhil_konduru libvirt-qemu
  sudo getent group | grep libvirt 
```


![getent](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/getent.png)


```
   sudo virsh list --all
```


![getent](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/virshlist-all.png)


```
   sudo systemctl status libvirtd 
``` 


![getent](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/systemctl.png)

19. As everything is functioning properly, the output returns an active (running) status.
Use virt-manager to create  inner VM along with the GUI :

```
cd ~
wget https://releases.ubuntu.com/jammy/ubuntu-22.04.1-desktop-amd64.iso
sudo mv ~/ubuntu-22.04.1-desktop-amd64.iso /var/lib/libvirt/images/
sudo virt-manager
```   

![getent](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/virt-manager.png)

20. choose create a new virtual machine  and select the os downloaded ubuntu 22.04.1 


21. Performed our exit handling testing on the newly installed inner-VM:


#### step 13: intialise two terminals(GCP Host VM , and nested VM terminal Ubuntu) and perform testing for the cpuid functionality : 

        Testing the CPUID functionality for '%eax=0x4ffffffe'      
        Ubuntu (Nested VM): sudo cpuid -l 0x4ffffffe
<img width="692" alt="image" src="">
        GCP host VM : sudo dmesg
<img width="693" alt="image" src="">

        Testing the CPUID functionality for '%eax=0x4fffffff'
         Ubuntu (Nested VM):: sudo cpuid -l 0x4fffffff
<img width="693" alt="image" src="">
           GCP host VM : sudo dmesg
<img width="668" alt="image" src="">

    


