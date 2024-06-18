# PurpleasFuck

### Prepare GOAD, VMWare and Vagrant
1. Download GOAD: https://github.com/Orange-Cyberdefense/GOAD
2. Download VMware Workstation Pro
3. Download Vagrant VMware Utility - https://developer.hashicorp.com/vagrant/docs/providers/vmware/vagrant-vmware-utility
4. Install the Vagrant plugin for VMWare: vagrant plugin install vagrant-vmware-desktop
5. Download Docker (maybe?)


### Setup VMWare Network
1. Open "Virtual Network Editor"
2. Create a new host-only network (e.g., vmnet2)
3. Disable DHCP
4. Save

### Edit network settings in Vagrantfile
1. Open the Vagrantfile under GOAD/ad/providers/vmware/Vagrantfile
2. Change the IP-adresses in the array called boxes: change them so that they are in the newly created VMware Network
3. Save


### Run GOAD
1. Navigate to GOAD-MAIN/ad/GOAD/providers/vmware
2. vagrant up
3. Done.


### Edit Network Settings for GOAD-machines
1. Set the network adapter to Custom and select vmnet2.


### Setup Zeek
1. Create new VM (any linux distro will do, but lightweight is better)
2. sudo apt-get update, sudo apt-get install zeek
3. Edit the Zeek configuration to listen on this interface: 
    sudo nano /usr/local/zeek/etc/node.cfg
4. Set the interface option to the correct network interface (e.g., eth1):
    [zeek]
    type=standalone
    host=localhost
    interface=eth1
5. sudo zeekctl deploy
6. Logs are typically located in /usr/local/zeek/logs/current.

### Add an Additional Network Adapter to the Zeek VM:
1. Edit the network settings of the Zeek VM.
2. Add a second network adapter and set it to Custom with vmnet2.
3. The first network adapter should remain connected to the default network for management access.





### Setup Open vSwitch (Virtual Network Tap)
1. Create new VM (any linux distro will do, but lightweight is better)
2. Download: sudo apt-get install openvswitch-switch
3. Create a new bridge and add the vmnet2 interface to it: 
    sudo ovs-vsctl add-br br0
    sudo ovs-vsctl add-port br0 vmnet2
4. Configure port mirroring in Open vSwitch to send all traffic from vmnet2 to the Zeek VM’s interface:
    sudo ovs-vsctl -- set Bridge br0 mirrors=@m -- \
    --id=@m create Mirror name=mirror0 select-all=true output-port=<zeek_interface>

3. Edit the network settings of the Zeek VM.
4. Add a second network adapter and set it to Custom with vmnet2.

