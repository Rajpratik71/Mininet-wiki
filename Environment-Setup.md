The following instruction will guide you through how to setup the environment for the [[programming assignment|Assignments]] on Ubuntu. We also provide an Amazon EC2 AMI image with everything set up for people would like to run the lab on EC2. 

##### Install Needed Tools
* sudo apt-get update
* sudo apt-get install -y git vim-nox python-setuptools flex bison traceroute

##### Install Mininet
* cd ~
* git clone git://github.com/mininet/mininet
* cd mininet
* git checkout remotes/origin/class/cs244
* ./util/install.sh -fnv

##### Install POX
* cd ~
* git clone http://github.com/noxrepo/pox

##### Install ltprotocol 
* cd ~
* git clone git://github.com/dound/ltprotocol.git
* cd ltprotocol 
* sudo python setup.py install
