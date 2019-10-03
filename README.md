# SSH to a VM in MacOs

The idea is to give you the step by step of how to use an Linux VM from your machine, without using any graphical interface.

This is ideal for people like me that need some particular Software like gnuradio (in my case), just to run and test code, and don't have a full linux machine, and havent been able to run a docker container with USB access (when i do that endebour i will post it too).

I gathered this steps by checking several guides and comments in the forums on how to do it. All am i doing is upating old guides and steps that had worked for me until today (October 3 2019)

1. you can create the virtual enviroment directly in VirtualBox and from there install your linux distro. I personally believe that is better the server editions, because of the work we will be doing, in which we won't need graphical interface.

2. You can also do it from the commmand line:

cd ~/VirtualBox\ VMs/

# Change these variables as needed
VM_NAME=“gnuradio”
UBUNTU_ISO_PATH=~/Downloads/ubuntu-18.04.1-server-amd64.iso
VM_HD_PATH="UbuntuServer.vdi" # The path to VM hard disk (to be created).
SHARED_PATH=~ # Share home directory with the VM


vboxmanage createvm --name $VM_NAME --ostype Ubuntu_64 --register
vboxmanage createhd --filename $VM_NAME.vdi --size 10768 #10 gigas
vboxmanage storagectl $VM_NAME --name "SATA Controller" --add sata --controller IntelAHCI
vboxmanage storageattach $VM_NAME --storagectl "SATA Controller" --port 0 --device 0 --type hdd --medium $VM_HD_PATH
vboxmanage storagectl $VM_NAME --name "IDE Controller" --add ide
vboxmanage storageattach $VM_NAME --storagectl "IDE Controller" --port 0 --device 0 --type dvddrive --medium $UBUNTU_ISO_PATH
vboxmanage modifyvm $VM_NAME --ioapic on
vboxmanage modifyvm $VM_NAME --memory 1024 --vram 128
vboxmanage modifyvm $VM_NAME --nic1 nat
vboxmanage modifyvm $VM_NAME --natpf1 "guestssh,tcp,,2222,,22"
vboxmanage modifyvm $VM_NAME --natdnshostresolver1 on
vboxmanage sharedfolder add $VM_NAME --name shared --hostpath $SHARED_PATH --automount

3. if the virtual enviroment is on, you can turn it off like this,

vboxmanage controlvm gnuradio poweroff

4. From the command line:

VBoxManage modifyvm gnuradio --natpf1 "ssh,tcp,,3022,,22"
VBoxManage showvminfo gnuradio | grep 'Rule'

5. initiate the virtual enviroment without the graphical intercafe:

vboxmanage startvm gnuradio --type headless

6. To make ssh to the server, first restart the VM and then:

ssh -p 3022 username@127.0.0.1

**GIT that works for me**

i personally don't mind that the username and password of my github or bitcucket is on plain text when i am in a VM, so i use:

git config --global credential.helper store


Any extra tips and comments are welcomed. 
Have a great day.
