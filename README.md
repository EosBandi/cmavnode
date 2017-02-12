# cmavnode
MAVLink forwarding node written in C++

For help with or discussions about cmavnode come to our gitter room. Issues and pull requests welcome.
https://gitter.im/cmavnode/cmavnode

This program can forward packets between an arbitrary number of MAVLink connections.
Supports UDP, and Serial.

Mavlink routing is done transparently. (cmavnode will not inject any packets)
cmavnode treats each link equally unless specified otherwise, and does not differentiate between an autopilot and a groundstation.

## Installing

- Clone the repository

- Update Git submodules 

         git submodule update --init

- Install dependencies

         Ubuntu 14.04: sudo apt-get install libboost-all-dev cmake libconfig++ libreadline-dev
         Ubuntu 16.04: sudo apt-get install libboost-all-dev cmake libconfig++-dev libreadline-dev
         Debian Stretch: sudo apt-get install libboost-all-dev cmake libconfig++-dev libreadline-dev
* Build cmavnode

         mkdir build && cd build
         cmake ..
         make
- OPTIONAL: Install cmavnode to /opt/cmavnode and add to path:

         sudo make install
         echo "alias cmavnode='/opt/cmavnode/cmavnode" >> ~/.bashrc

## Usage

    ./cmavnode -f <pathtoconfigfile>

cmavnode is configured by a config file. This config file specifies the links you want (socket or serial) and allows you to dictate routing rules. Please see examples to see how this works.

Use -i to get an interactive shell, type help into the shell to list commands.

## Config File
cmavnode uses a config file which defines the links it should create. Each link has several options, some of which are optional.

A link is defined by the following syntax

        [linkname]
            option1=.....
            option2=.....

### Serial Port
This is the minimum configuration needed for a serial port

        [linkname]
            type=serial
            port=/dev/ttyUSB0
            baud=57600

By default hardware flow control (RTS,CTS) is disabled, to enable it use:

        flow_control=true

### UDP
UDP can operate in different ways.

#### Fully Specified
The first simple way is to specify all the connection information, as follows.

        [linkname]
            type=socket
            targetip=127.0.0.1
            targetport=14553
            localport=14550

#### UDP Server
Specify only the localport. Cmavnode will lock onto the first endpoint that sends to it.

        [linkname]
            type=socket
            localport=14550

#### UDP Client
Specify only targetip and targetport, and the local port will be asigned by the kernel. If you don't specify target ip it will default to "localhost".

        [linkname]
            type=socket
            targetip=192.168.1.1
            targetport=14550
            
#### UDP Broadcast
Will broadcast to a specified broadcast address using a specified port. You can choose to bind on a specific ip rather than 0.0.0.0, this will only affect whether you receive from all interfaces.
By default the link will broadcast to every device, until a device responds, then the link will stop broadcasting and turn into a normal udp socket with the device. (Like mavproxy does with udpbcast option)
If you want to keep broadcasting and support connections to multiple devices, set bcastlock to false.

        [linkname]
            type=udpbcast
            bcastip=192.168.0.255
            bcastport=14553
            bcastlock=false #optional, default true
            bindip=192.168.0.30 #optional, default 0.0.0.0

### Optional Flags
The following flags can be applied to any type of link and are optional
        
        sim_enable=true #enables simulation options
        sim_packet_loss=25 #simulates packet loss of 25% on this link (incoming and outgoing)
        output_only_from=1,2,3 #only sends packets from sysID's 1, 2, and 3 on this link
        reject_repeat_packets=true #enables detection and removal of duplicate packets from different links
        sik_radio=true #enable this to be able to see radio stats (rssi, noise etc) on the console interface


## Licence
Cmavnode is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

Cmavnode is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with Cmavnode. If not, see http://www.gnu.org/licenses/.
