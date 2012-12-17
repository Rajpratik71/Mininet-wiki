The following instruction will guide you through how to setup the environment to use Mininet for the [[programming assignment|Assignments]] on Ubuntu. We also provide an Amazon EC2 AMI image with everything set up for people would like to run the lab on EC2. 

##### Install Needed Tools
* sudo apt-get update
* sudo apt-get install -y git vim-nox python-setuptools flex bison

##### Install Mininet
* cd ~
* git clone git://github.com/mininet/mininet
* cd mininet
* git checkout remotes/origin/class/cs244
* ./util/install.sh -fnv

##### Install POX
* cd ~
* git clone http://github.com/noxrepo/pox

##### install ltprotocol 
* git clone git://github.com/dound/ltprotocol.git
* cd ltprotocol 
* sudo python setup.py install


6. checkout code
git clone https://huangty@bitbucket.org/huangty/cs144_lab3.git
cd cs144_lab3/
git checkout --track remotes/origin/standalone

7. install pox module
cd cs144_lab3
./config

8. create http server home directory
mkdir ~/http_server2
mkdir ~/http_server1