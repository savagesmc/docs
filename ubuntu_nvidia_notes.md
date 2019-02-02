# Random Notes on NVIDIA and Ubuntu

## Setup NVidia 10 Drivers & Cuda

Taken from [HowTo Install CUDA 10](https://www.pugetsystems.com/labs/hpc/How-To-Install-CUDA-10-together-with-9-2-on-Ubuntu-18-04-with-support-for-NVIDIA-20XX-Turing-GPUs-1236/)

**Note**: Installing the CUDA repo shown below **will also install the NVidia drivers** (410.48 as of now).  Before running this, make sure that all the previous NVidia Drivers and Cuda packages have been uninstalled.

Install CUDA Dependencies
```
sudo apt-get install build-essential dkms
sudo apt-get install freeglut3 freeglut3-dev libxi-dev libxmu-dev
```

Get the Cuda .deb file for installation.
```
wget https://developer.nvidia.com/compute/cuda/10.0/Prod/local_installers/cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64
mv cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64 cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb
sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda
```
This will pull down and install all of the CUDA 10 packages along with dependencies **AND the 410 NVIDIA display driver.**

**Now reboot.** When your system restarts you will be running the NVIDIA 410 display driver and have CUDA 10 installed in /usr/local.

## Fixing .run clash with modules
During the process, if you aren't paying attention, you may accidental install one of the nvidia driver .run files, or
get some kind of nvidia contamination that will result in an [Nvidia API mismatch](https://askubuntu.com/questions/50469/nvidia-api-mismatch)
type of error.

First ssh into the box, and disable graphics via
```
service gdm stop
```
Then uninstall all nvidia and cuda packages via
```
sudo -s
apt list -i | grep -i nvidia | cut -d"/" -f 1 | xargs sudo apt remove -y
apt list -i | grep -i cuda | cut -d"/" -f 1 | xargs sudo apt remove -y
```

Remove the straggler file `/usr/lib/nvidia/pre-install`.

`sudo /usr/sbin/dmks status` should not return any nvidia modules installed.  If it does, remove via
```
sudo /usr/bin/dkms uninstall nvidia/410.48
```
 
