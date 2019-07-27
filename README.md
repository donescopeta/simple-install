# simple-install
Ansible-like BASH micro installation framework.

## Usage
The info file is divided to section by a `##SECTION` phrase. Each section is one single step of the process. Each section can contain subsections for different operations, for example:


* `##COMMENT` - Contains a comment.
* `##DEFINE` - Contains a nessesery variables.
* `##INSTALL` - Contains BASH script that will be executed on `install` operation. 
* `##UNINSTALL`- Contains BASH script that will be executed on `uninstall` operation. 
* `##DATA:<variable name>` Contains text data that will be assinged to a variable 
## Help
```
install.sh Simple installator which uses an info file.
Usage : install.sh [NAME OF A SUBSECTION IN INFO FILE] [options]
	-h			This help text.
	-a			Don't ask to do a step.
	-v			Show the commands which will be executed.
	-s 		step	Do step certain.
	-l			List sections and its comments.
	-c			Use a config file.
	-i 			Use a specified instalation file.
Example :
	install.sh INSTALL
	install.sh UNINSTALL -v
```


Example script:

```
##SECTION main
	##COMMENT
		Instalacja kontenera dystrybucji alpine oraz pakietow servera asteriask.
		Do przeprowadzienia instalacji jest wymagane połaczenie z internetem
	##DEFINE
		CONTAINER_NAME = alpine-asterisk
		CONTAINER_IP = 192.168.0.237
		HOSTIP = 192.168.0.236
		GATEWAY = 192.168.0.7
		DNS = 192.168.0.7
		ROOTDIR = /var/lib/lxc/${CONTAINER_NAME}/rootfs
	##INSTALL
		#echo "Czy chcesz rozpocząć?"
#		ifconfig | grep -q ethbr1 && \
#			JUMP configure_alpine
			
			
##SECTION packeges
	##COMMENT
		Instalacja niezbędnych pakietów.
	##DEFINE
		PACKAGES = bridge-utils lxc
	##INSTALL
		sudo apt-get install $PACKAGES
	##UNINSTALL
		sudo apt-get remove $PACKAGES

##SECTION lxc_container
	##COMMENT
		Instalacja kontenera dystrybucji alpine linux
	##INSTALL
		lxc-create --name $CONTAINER_NAME -t alpine -- --release edge
	##UNINSTALL
		lxc-destroy $CONTAINER_NAME
		

##SECTION lxc_container_options
	##DATA:LXCSETTINGS
		#lxc_net
		lxc.network.type = veth
		lxc.network.link = ethbr1
		lxc.network.flags = up
		lxc.network.name = eth0
		lxc.network.ipv4 = ${CONTAINER_IP}/24
		lxc.network.ipv4.gateway = ${GATEWAY}
		lxc.start.auto = 1
		#__lxc_net
	##COMMENT
		Konfiguracja ustawień sieci kontenera
	##INSTALL
		#incat LXCSETTINGS
		incat LXCSETTINGS >> /var/lib/lxc/${CONTAINER_NAME}/co
```
