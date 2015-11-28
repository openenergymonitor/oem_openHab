OpenEnergyMonitor config for openHAB


com/user/downloadSubjectPublicKey?username=openhab' | sudo apt-key add -
	echo "deb http://dl.bintray.com/openhab/apt-repo stable main" | sudo tee /etc/apt/sources.list.d/openhab.list
	sudo apt-get update
	sudo apt-get install openhab-runtime
	sudo /etc/init.d/openhab start


oem.items > /etc/openhab/configurations/items
oem.sitemap > /etc/openhab/configurations/sitemaps

http://IP_ADDRESS:8080/openhab.app?sitemap=oem

add to 

In configurations/openhab.cfg in the MQTT section add:

mqtt:mosquitto.url=tcp://locahost:1883


