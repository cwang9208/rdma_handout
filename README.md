**Setting up the RDMA Framework for Development**  
  
1. Installing kernel source packages    
`sudo apt-get install libtool autoconf automake linux-tools-common`  
`sudo apt-get install fakeroot build-essential crash kexec-tools makedumpfile kernel-wedge`  
`sudo apt-get build-dep linux`  
`sudo apt-get install git-core libncurses5 libncurses5-dev libelf-dev`  
`sudo apt-get install linux-headers-$(uname -r)`  

2. Installing RDMA dependencies  
`sudo apt-get install libibverbs1 libibcm1 libibcm-dev ibverbs-utils libibverbs-dev`  
`sudo apt-get install libibverbs1 librdmacm-dev librdmacm1 rdmacm-utils`  
  
3. Installation of the udev Rules File for Ubuntu Systems  
Copy following udev rules file in proper location to make sure that your system will create RDMA related devices in /udev/infiniband/ correctly when proper kernel modules are inserted.  
`sudo cp rmda_handout/90-ib.rules /etc/udev/rules.d/`  
Typically, udev will detect the new rules and incorporate them automatically. You may have to Reboot your system in order for this modification to take effect (or restart the udev manager) if it does not work for you directly.  
  
4. Building the SoftiWARP Kernel Driver  
The SoftiWARP RDMA device comes with a kernel module and a user space library. You can build the SoftiWARP kernel driver as follows:  
Go into the rdma_handout/siw/kernel/softiwarp that exists under the directory you extracted the softiwarp code. Build it using:  
`make clean`  
`make`  
`sudo make install`  

5. Building the SoftiWARP userspace libary  
Then go to the rdma_handout/siw/userlib/libsiw-0.1 directory inside your extracted softiwarp directory and build the SoftiWARP user library as follows:  
`./autogen.sh`  
`./autogen.sh`  
`./configure`  
`make clean`  
`sudo make install`  
`sudo ldconfig`  
Also, you need to make sure that the siw.driver file placed correctly in your system’s /etc/libibverbs.d directory. You can do that by creating following soft link to the default installation directory (shown bellow), or by copying the file directly in the /etc/libibverbs.d directory.  
`sudo ln -s /usr/local/etc/libibverbs.d /etc/libibverbs.d`  

6. Loading Kernel Modules  
To load all required modules into the system, we have provided a script called addmodules.sh in the folder. This script is fairly simple and tries to make sure that all the requirements are met.  
At this point you should be able to see the siw module loaded in your system. You can verify this by doing `lsmod | grep "siw"`.

7. Verify  
To verify if the system is ready for the RDMA development you should be able to see a few RDMA capable devices using command `ibv_devices`.  
  
8. Debugging  
For debugging there is a compile time macro `debug` in rdma_common.h. Enable it by defining `ACN_RDMA_DEBUG`.   
To see what is happening at the device level, you can compile siw module with `DPRINT_MASK` defined to `DBG_ALL` in siw_debug.h file. The output of the device can be seen with `dmesg` command. These messages can show you common mistakes such as base and bound violations, permission errors, invalid stags etc.  