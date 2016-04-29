# OpenEnergyMonitor skeleton config for openHAB

Blog Post: http://openenergymonitor.blogspot.com/2015/12/openenergymonitor-emonpi-and-openhab.html

![OpenEnergyMonitor_openHAB](images/web.png)

# To install openHab on emonPi / Raspberry Pi:

Check Java version (JVM 1.6 or later is required):

	$ java - version
	
Install if needed:

	$ sudo apt-get install oracle-java7-jdk

Install OpenHab:
	
	$ rpi-rw
	$ wget -qO - 'https://bintray.com/user/downloadSubjectPublicKey?username=openhab' |sudo apt-key add -
	$ echo "deb http://dl.bintray.com/openhab/apt-repo stable main" | sudo tee /etc/apt/sources.list.d/openhab.list
	$ sudo apt-get update
	$ sudo apt-get install openhab-runtime
	$ sudo /etc/init.d/openhab start

To run openHab at startup:

	sudo systemctl daemon-reload
	sudo systemctl enable openhab
	sudo nano /etc/rc.local
	
add

	/etc/init.d/openhab start

before 'exit 0'

# Install MQTT & HTTP Bindings

	sudo apt-get install openhab-addon-binding-mqtt
	sudo apt-get install openhab-addon-binding-http


# Install the OpenEnergyMonitor config files:

	$ git clone https://github.com/openenergymonitor/oem_openHab
	$ sudo ln -s /home/pi/oem_openHab/openhab.cfg /etc/openhab/configurations/
	$ sudo ln -s /home/pi/oem_openHab/oem.items /etc/openhab/configurations/items/default.items
	$ sudo ln -s /home/pi/oem_openHab/oem.sitemap /etc/openhab/configurations/sitemaps/default.sitemap
	$ sudo /etc/init.d/openhab restart

Then browse to:

	http://IP_ADDRESS:8080

You might need to open up the port:

	sudo ufw allow 8080/tcp

	
# Enable Authentication

	sudo nano /etc/openhab/configurations/openhab.cfg
Change:
	security:option=ON 
	
or to only enable authentication required for external connections 
	
	security:option=EXTERNAL 

You might also need to set netmask to inidcate requests from your local network, this setting defaults to `192.168.1.0/24`, however my home network uses 192.168.0.XX addresses, therfore I needed to set: 

	security:netmask=192.168.0.0/24
	
Add authentication details:

	sudo nano /etc/openhab/configurations/users.cfg
	
Add "user=password" to users.cfg

	pi = emonpi2016
	
# Read-only filesystem

If you want to run openHAB on emonPi with read-only file system you will need to mount /var/lib/openhab as tempfs in RAM
	
	$ sudo sh -c "echo 'tmpfs           /var/lib/openhab   tmpfs   nodev,nosuid,size=20M,mode=1777        0    0' >> /etc/fstab"
	
Also the correct ports will ned to be opend and RAM tmpfs log file created on startup. At the following to /etc/rc.local
	
	sudo iptables -A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT
	sudo mkdir /var/log/openhab
	sudo chmod 666 /var/log/openhab
	/etc/init.d/openhab start
	
# Debugging

View log:

	$ tail /var/log/openhab/openhab.log
	$ tail /var/log/openhab/event.log
		
To enable verbose debug mode cheange debug to 'yes' in:

	sudo nano /etc/default/openhab
	
Note: it's not recomended to leave debug turned on by default as its very verbose and will fill up your logs!

By default openHAB logs all events to  /var/log/openhab/event.log, this is very verbose and filles up /var/log quickly as events also appear in syslog and daemon log. To disable event log:

	sudo nano /etc/openhab/logback.xml

Change "runtime.busevents" loglevel from "INFO" to "WARN"

	<logger name="runtime.busevents" level="WARN" additivity="false">
	
The number of log archives that openHAB keeps can also be reduced by editing `/etc/openhab/logback.xml` and `/etc/openhab/logback_debug.xml` On emonPi opneHAB has been set to keep only a single log archive as oposed the the default 30!
	
# Remote Access

There are two options to access openHAB remotly: 

## 1. Direct Access
To access openHAB remotly you will need to open port 8080 on your router. If you have a dynamic IP you will need to setup a Dynamic DNS service such as [DuckDNS](https://www.duckdns.org/install.jsp) or [NoIP](http://www.noip.com/support/knowledgebase/install-ip-duc-onto-raspberry-pi/).

## 2. OpenHab cloud

OpenHab have made remote access easier by provding a cloud service: [my.openHAB.org](https://my.openhab.org/). For fully setup inf see [my.openhab docs](https://my.openhab.org/docs). Steps required for emonPi are listed below: 

	sudo apt-get install openhab-addon-io-myopenhab
	sudo service openhab restart
	
Create account on [my.openHAB.org](https://my.openhab.org/) using `UUID` and `secret` from:

	cat /usr/share/openhab/webapps/static/uuid
	cat /usr/share/openhab/webapps/static/secret

Browse to:

https://my.openhab.org/openhab.app







