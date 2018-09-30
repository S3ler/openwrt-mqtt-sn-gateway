# openwrt-mqtt-sn-gateway
tested with Ubuntu 18.04 x86_64.

## Build MQTT-SN Gateway

### Initialize Environment
We assue you have ~/git directory containing the linux-mqtt-sn-gateway source files.

If not got make:

	mkdir -p ~/git
	git clone --recursive https://github.com/S3ler/linux-mqtt-sn-gateway.git ~/git/linux-mqtt-sn-gateway
	git clone https://github.com/S3ler/openwrt-mqtt-sn-gateway.git ~/git/openwrt-mqtt-sn-gateway

Then copy `~/git/openwrt-mqtt-sn-gateway/feeds.conf` into your `openwrt/sdk directory`.

## Compile
go into your `openwrt/sdk directory` and update and install the feed by executing:
(Like in this tutorial step: https://openwrt.org/docs/guide-developer/helloworld/chapter4)

	./scripts/feeds update mqttsngateway
	./scripts/feeds install -a -p mqttsngateway

Then configurate the environment (like in this tutorial step: https://openwrt.org/docs/guide-developer/helloworld/chapter5):
Run `make menuconfig`, and select the `MQTT-SN` sub-menu. Highlight the `mqttsngateway` entry underneath this menu, and click on the `Y` key to include this package into the firmware configuration.  `Exit` the menu, `saving your changes`.
You can then build the package by issuing the following command: 

	make package/udpmqttsngateway/compile
	
If everything went successfully, we are presented with a brand new package named `mqttsngateway1.0-1_<arch>.ipk in bin/packages/<arch>/mypackages` folder. 
Then upload the ikp.file to your your device and install:

	root@OpenWrt:/# opkg install mqttsngateway1.0-1_<arch>.ipk

Next add a `MQTT.CON` and a `MQTTSN.CON` file into your home-directory.
Example [MQTT.CON](https://github.com/S3ler/linux-mqtt-sn-gateway/blob/master/MQTT.CON) file:

	brokeraddress 192.168.178.33
	brokerport 1883
	clientid mqtt-sn linux gateway
	willtopic /mqtt/sn/gateway
	willmessage /mqtt-sn linux gateway offline
	willqos 1
	willretain 0
	gatewayid 2

Example [MQTTSN.CON](https://github.com/S3ler/linux-mqtt-sn-gateway/blob/master/MQTTSN.CON) file:

	gatewayid 2
	timeout 10
	advertise 900

And start the mqtt-sn-gateway:

	root@OpenWrt:/# mqttsngateway

If everything worked correctly you should see:

	root@OpenWrt:/# mqttsngateway
	
	0: Linux MQTT-SN Gateway version 0.0.1a starting
	0: Persistent ready
	0: loading timeout check duration
	0: loading advertise duration
	0: loading gateway id
	0: loading Mqtt configuration
	0: Mqtt configuration loaded
	0: loading Mqtt will
	0: Mqtt will loaded
	0: Gateway ready
	

