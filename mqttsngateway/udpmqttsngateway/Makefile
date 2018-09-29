include $(TOPDIR)/rules.mk

# Name, version and release number
# The name and version of your package are used to define the variable to point to the build directory of your package: $(PKG_BUILD_DIR)
PKG_NAME:=mqttsngateway
PKG_VERSION:=1.0
PKG_RELEASE:=1

# Source settings (i.e. where to find the source codes)
# This is a custom variable, used below
#SOURCE_DIR:=/home/bele/openwrt/helloworld
SOURCE_DIR:=home/bele/git/openwrt-mqtt-sn-gateway/linux-mqtt-sn-gateway

CORE_DIR := src/core-mqtt-sn-gateway/src
CORESRCS := \
$(PKG_BUILD_DIR)/$(CORE_DIR)/CoreImpl.cpp \
$(PKG_BUILD_DIR)/$(CORE_DIR)/Gateway.cpp \
$(PKG_BUILD_DIR)/$(CORE_DIR)/MqttMessageHandlerInterface.cpp \
$(PKG_BUILD_DIR)/$(CORE_DIR)/PersistentInterface.cpp \
$(PKG_BUILD_DIR)/$(CORE_DIR)/SocketInterface.cpp 

IMPLEMENTATION_DIR := src/Implementation
IMPLEMENTATIONSRCS := \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/LinuxGateway.cpp \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/LinuxLogger.cpp \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/LinuxPersistent.cpp \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/Linuxsystem.cpp 

MQTTCLIENT_DIR := paho/pahomqttembeddedc
MQTTCLIENTSRCS := \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTConnectClient.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTConnectServer.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTDeserializePublish.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTFormat.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTPacket.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTSerializePublish.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTSubscribeClient.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTSubscribeServer.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTUnsubscribeClient.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/MQTTUnsubscribeServer.c \
$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTClient/src/linux/linux.cpp 

ARDUINOLINUXABSTRACTION_DIR := src/arduino-linux-abstraction/src
ARDUINOLINUXABSTRACTIONSRCS := \
$(PKG_BUILD_DIR)/$(ARDUINOLINUXABSTRACTION_DIR)/Arduino.cpp \
$(PKG_BUILD_DIR)/$(ARDUINOLINUXABSTRACTION_DIR)/SD.cpp 

TRANSMISSION_PROTOCOL := Udp
TRANSMISSIONPROTOCOLSRCS := $(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(TRANSMISSION_PROTOCOL)/LinuxUdpSocket.cpp


# change MAINSRCS if you want to provide your own main
MAIN_DIR := src/main
MAINSRCS := $(PKG_BUILD_DIR)/$(MAIN_DIR)/main.cpp

CSRCS := \
$(CORESRCS) \
$(IMPLEMENTATIONSRCS) \
$(MQTTCLIENTSRCS) \
$(PKG_BUILD_DIR) \
$(TRANSMISSIONPROTOCOLSRCS) \
$(MAINSRCS) 

INCLUDES += \
-I$(PKG_BUILD_DIR)/ \
-I$(PKG_BUILD_DIR)/$(CORE_DIR)/ \
-I$(PKG_BUILD_DIR)/$(ARDUINOLINUXABSTRACTION_DIR)/ \
-I$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/ \
-I$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(TRANSMISSION_PROTOCOL)/ \
-I$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(TRANSMISSION_PROTOCOL)/ \
-I$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTPacket/src/ \
-I$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTClient/src/ \
-I$(PKG_BUILD_DIR)/$(IMPLEMENTATION_DIR)/$(MQTTCLIENT_DIR)/MQTTClient/src/linux 

LDADD := -lpthread

include $(INCLUDE_DIR)/package.mk

# Package definition; instructs on how and where our package will appear in the overall configuration menu ('make menuconfig')
define Package/mqttsngateway
  SECTION:=mqtt-sn
  CATEGORY:=MQTT-SN
  TITLE:=MQTT-SN Gateway
  DEPENDS:=+libpthread
  URL:=https://github.com/S3ler/mqtt-sn-gateway
endef

# Package description; a more verbose description on what our package does
define Package/mqttsngateway/description
  A aggregating MQTT-SN Gateway using UDP for MQTT-SN. For more information see https://github.com/S3ler/mqtt-sn-gateway .
endef

# Package preparation instructions; create the build directory and copy the source code. 
# The last command is necessary to ensure our preparation instructions remain compatible with the patching system.
define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	cp $(SOURCE_DIR)/* $(PKG_BUILD_DIR)
	$(Build/Patch)
endef

# Package build instructions; invoke the target-specific compiler to first compile the source file, and then to link the file into the final executable
define Build/Compile
	$(TARGET_CC) $(TARGET_CFLAGS) -o $(PKG_BUILD_DIR)/mqttsngateway.o -c $(PKG_BUILD_DIR)/helloworld.c
	$(TARGET_CC) $(TARGET_LDFLAGS) -o $(PKG_BUILD_DIR)/$1 $(PKG_BUILD_DIR)/mqttsngateway.o
endef

# Package install instructions; create a directory inside the package to hold our executable, and then copy the executable we built previously into the folder
define Package/mqttsngateway/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/mqttsngateway $(1)/usr/bin
endef

# This command is always the last, it uses the definitions and variables we give above in order to get the job done
$(eval $(call BuildPackage,mqttsngateway))
