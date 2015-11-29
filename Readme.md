# OpenEnergyMonitor skeleton config for openHAB

	$ wget -qO - 'https://bintray.com/user/downloadSubjectPublicKey?username=openhab' |sudo apt-key add -
	$ echo "deb http://dl.bintray.com/openhab/apt-repo stable main" | sudo tee /etc/apt/sources.list.d/openhab.list
	$ sudo apt-get update
	$ sudo apt-get install openhab-runtime
	$ sudo /etc/init.d/openhab start

Install the oem config files:

	git clone this repo
	cd oem_openHab
	sudo cp oem.items /etc/openhab/configurations/items
	sudo cp oem.sitemap /etc/openhab/configurations/sitemaps
	 sudo /etc/init.d/openhab restart

Then browse to: 

http://IP_ADDRESS:8080/openhab.app?sitemap=oem

You might need to open up the port:

	sudo iptables -A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT

To save port rules use:

	sudo apt-get install iptables-persistent
	sudo nano /etc/iptables/rules.v4


# Install MQTT & HTTP Bindings

Download openHAB addons (http://www.openhab.org/getting-started/downloads.html), unzip and extract. You probably want to do this on your PC since download .zip is over 80Mb then copy te required add on's

	sudo cp org.openhab.binding.mqtt-1.7.1.jar /etc/openhab/addons/
	sudo apt-get install openhab-addon-binding-mqtt
	
	sudo cp org.openhab.binding.http-1.7.1.jar /etc/openhab/addons/
	sudo apt-get install openhab-addon-binding-http

In configurations/openhab.cfg in the MQTT section add:

	mqtt:mosquitto.url=tcp://locahost:1883
	
# Enable Authentication

	sudo nano /etc/openhab/configuration/openhab.cfg
Change:  
	security:option=ON or EXTERNAL for external from WAN security only

	sudo nano /etc/openhab/configuration/users.cfg
	
Add "user=password" e.g:

	pi = raspberry



