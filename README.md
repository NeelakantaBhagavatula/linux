Assignment 1: To discover VMX features

Team - Neelakanta Bhagavatula (SID: 015261909)

Steps followed for Assignment 1:
  1. Install VMWare Workstation on windows.
  2. Install Ubuntu 20.04.3 in VMWare Workstation by allocating 8 VCPU's, 9 GB RAM and 200 GB disk space.
  3. Fork the master branch of linux repository from https://github.com/torvalds/linux.
  4. Install Git using command "sudo apt install git".
  5. Ensure git is installed using "git --version". If version is not displayed, try update command "sudo apt-get update" and reinstall git.
  6. Configure git using following commands
      - git config --global user.name "<your_github_username"
      - git config --global user.email "<your_github_email_address>"
  7. Install below packages for kernel compilation
      - sudo apt install htop
  8. Clone the forked repository into your local machine created using VMWare Workstation using git clone command (Note: Install Git if not installed already).
  9. Steps for cloning
      - Go to github.com and point the repository to linux.
      - Click on Code button that appears and copy the URL for HTTPS.
      - Change the working directory where you want to work with the repo and type command "git clone https://github.com/NeelakantaBhagavatula/linux.git".
      - Ensure git is pointing to the forked repo and not the torvalds/linux repo.
      - Git downloads the code and resolves dependencies from the link pointed.
  10. During compilation, I got some of the errors but resolved it by installing below packages. These are the list of libraries required for compiling and installing kernel code
      - sudo apt-get update
      - sudo apt-get upgrade
      - sudo apt-get install build-essential
      - sudo apt-get install libelf-dev
      - sudo apt-get install flex
      - sudo apt-get install bison
      - sudo apt-get install libncurses5-dev gcc make git exuberant-ctags bc libssl-dev
      - sudo apt install dwarves
      - sudo apt-get update y
      - sudo apt-get install -y zstd
  11. Steps for building kernel
      - Navigate to linux folder
      - Type Command "make menuconfig" to view the interactive shell for loading kernel configuration (Nothing to be done/selected here, it displays configuration menu)
      - Exit from menu config
      - Check for current kernel version using command "uname -a"
      - To avoid version conflict/mismatch, copy the current kernel config file to a new config file in current (inside linux folder) directory using below command
      - cp /boot/config-5.13.0-39-generic .config
      - Type command "make oldconfig"
      - Type command "make prepare"
      - While running "make prepare" command, there were some error related to certificates. I resolved it using below steps
          - Created file named "x509.genkey" under /linux/certs
          - Contents of x509.genjey file are below

                [ req ]
                default_bits = 4096
                distinguished_name = req_distinguished_name
                prompt = no
                string_mask = utf8only
                x509_extensions = myexts

                [ req_distinguished_name ]
                O = neelakanta
                CN = neelakanta signing key
                emailAddress = XXXobscuredXXX

                [ myexts ]
                basicConstraints=critical,CA:FALSE
                keyUsage=digitalSignature
                subjectKeyIdentifier=hash
                authorityKeyIdentifier=keyid
             
          - Created file names "signing_key.pem" under /linux/certs
            - Type command "openssl req -new -nodes -utf8 -sha512 -days 36500 -batch -x509 -config x509.genkey -outform DER -out signing_key.x509 -keyout signing_key.pem" to create signing_key.pem file
          - Created file named "canonical-certs.pem" under /linux/debian
            - Refer site https://salsa.debian.org/kernel-team/linux/-/blob/master/debian/certs/debian-uefi-certs.pem for contents of canonical-certs.pem file
          - Created file names "canonical-revoked-certs.pem" under /linux/debian
            - Refer site https://salsa.debian.org/kernel-team/linux/-/blob/master/debian/certs/debian-uefi-certs.pem for contents of canonical-revoked-certs.pem file
      - Make modules using "make -j 8 modules" (Replace 8 with the number of vcpu's allocated during VM creation)
      - Type command "make -j 8" for making kernel
      - Type command for installing modules "sudo make INSTALL_MOD_STRIP=1 modules_install" (Note: INSTALL_MOD_STRIP=1 is used to skip debugging information)
      - Type command "sudo make install" to install our new kernel
      - Type command "sudo reboot" to restart the VM inorder to apply our changes
  12. After rebooting is complete, check the kernel version using "uname -a". If the kernel version bumped up to latest version along with the timestamp, then the kernel is installed properly and running (Note: In my machine, version changed from "5.13.0-39-generic" to "5.17.0+" and timestamp "Linux ubuntu 5.17.0+ #2 SMP PREEMP_DYNAMIC Sat Apr 2 20:35:25 PDT 2022 x86_64 x86_64 x86_64 GNU/Linux")
  13. Create a folder named cmpe283 inside linux folder and add cmpe283-1.c and Make file to this cmpe283 folder.
  14. Navigate to cmpe283 folder and type "make" to generate kernel object file for cmpe283-1.c (Note: Add MODULE_LICENSE("GPL v2"); if there is error related to license).
  15. Verify if the object file is created using "ls |grep *.ko"
  16. Next step is to insert the cmpe283-1.ko into kernel. Use below steps to do that
      - sudo insmod cmpe283-1.ko
      - Check if the module is inserted using "lsmod | grep cmpe283". It should display cmpe283_1.
  17. Finally, use "dmesg" command to display vmx features.

Output Screenshots

![1](https://user-images.githubusercontent.com/98799930/162285713-80e7942a-f4fc-408c-bb78-f147b3f8f538.png)
![2](https://user-images.githubusercontent.com/98799930/162285736-58108907-efd2-4a4b-b51e-711bb90de543.png)
![3](https://user-images.githubusercontent.com/98799930/162285750-45bc5137-f8b0-4942-a032-e6a238f7ee2b.png)

  19. Commit cmpe283-1.c and Makefile to the repository.


Assignment 2: Instrumentation via hypercall

Team - Neelakanta Bhagavatula (SID: 015261909)

Steps followed for Assignment 2:
  1. Modified cpuid.c & vmx.c to support exits for CPUID leaf nodes (0x4fffffff & 0x4ffffffe).
  2. Below are steps followed to build kvm module and install kernel
      - Type command "sudo make -j 8 modules"
      - Type command "sudo make INSTALL_MOD_STRIP=1 modules_install"
      - Type command "sudo make install"
      - Removing existing kvm modules using command "sudo rmmod kvm_intel" followed by "sudo rmmod kvm"
      - Loading kvm module again using command "sudo modprobe kvm" followed by "sudo modprobe kvm_intel"
      - Verified if kvm module is properly loaded using "lsmod | grep kvm"
  3. Created an Ubuntu VM instance inside existing VM by installing virtual manager using following command
      - sudo apt update
      - sudo apt install cpu-checker
      - To check if a VM can be created inside an existing VM, command used is "kvm-ok"
      - Installed virtual manager libraries using "sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager"
      - Verified if libcirt daemon is active using "sudo systemctl is-active libvirtd"
      - Added user group permissions using "sudo usermod -aG kvm $USER", "sudo usermod -aG libvirt $USER" and "newgrp libvirt"
  4. Started virtual manager using "virt-manager"
  5. Installed cpuid package manager inside inner VM using "sudo apt-get update -y" followe by "sudo apt-get install -y cpuid"
  6. Run the following command to check for cpuid leaf node exits
      - cpuid -l 0x4fffffff
      - cpuid -l 0x4ffffffe (Note: -l indicates the values of eax to be loaded)
  7. Output can be viewed in outer VM message buffer using "dmesg" command

Output Screenshots

![4](https://user-images.githubusercontent.com/98799930/162828242-046589b1-909f-4e87-b896-d485754d1fee.png)
![5](https://user-images.githubusercontent.com/98799930/162828259-b0577bec-9d69-4894-9520-09fcd355e3e3.png)
![6](https://user-images.githubusercontent.com/98799930/162828265-9dd0007e-5981-40c3-90ef-e910e6a4c64e.png)


  8. Pushed changes for vmx.c & cpuid.c files to the repository.


Assignment 2: Instrumentation via hypercall (cont'd)

Team - Neelakanta Bhagavatula (SID: 015261909)

Steps followed for Assignment 3:
  1. Modified cpuid.c & vmx.c to support exits for CPUID leaf nodes (0x4fffffff & 0x4ffffffe).
  2. Below are steps followed to build kvm module and install kernel
      - Type command "sudo make -j 8 modules"
      - Type command "sudo make INSTALL_MOD_STRIP=1 modules_install"
      - Type command "sudo make install"
      - Removing existing kvm modules using command "sudo rmmod kvm_intel" followed by "sudo rmmod kvm"
      - Loading kvm module again using command "sudo modprobe kvm" followed by "sudo modprobe kvm_intel"
      - Verified if kvm module is properly loaded using "lsmod | grep kvm"
  3. Started virtual manager using "virt-manager"
  4. Run the following command to check for cpuid leaf node exits
      - cpuid -l 0x4ffffffd -s #{exit_type}
      - cpuid -l 0x4ffffffc -s #{exit_type} (Note: -l indicates the values of eax to be loaded and -s indicates the value of ecx (exit_type) to be loaded)
  5. Output can be viewed in message buffer using "dmesg" command

Output Screenshots

![7](https://user-images.githubusercontent.com/98799930/163021877-b08fa774-7eff-4b08-ab83-ea0f720a0f86.png)
![8](https://user-images.githubusercontent.com/98799930/163021896-9e2e7949-39fd-4d60-83f9-fa42711c1382.png)
![9](https://user-images.githubusercontent.com/98799930/163021910-1bc1f79e-8d5e-4563-b111-010baa74782b.png)
![10](https://user-images.githubusercontent.com/98799930/163021923-042c4c52-b441-41fa-9700-ed3b040f79b2.png)

  6. Committed changes for vmx.c & cpuid.c files to the repository.

Below are some of the most frequent exits happened:
  1. Exit type 1 - External Interrupt
  2. Exit type 10 - CPUID
  3. Exit type 12 - HLT
  4. Exit type 30 - I/O instruction
  5. Exit type 32 - WRMSR
  6. Exit type 48 - EPT violation

Below are some of the least frequent exits happened:
  1. Exit type 0 - Exception or non-maskable interrupt (NMI)
  2. Exit type 7 - Interrupt window
  3. Exit type 28 - Control-register access
  4. Exit type 29 - MOV DR
  5. Exit type 31 - RDMSR
  6. Exit type 40 - PAUSE
  7. Exit type 49 - EPT misconfiguration
  8. Exit type 54 - WBINVD or WBNOINVD
  9. Exit type 55 - XSETBV
