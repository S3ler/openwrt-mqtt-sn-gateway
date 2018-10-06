# Patching Dragino LoRa Gateway openwrt-cc-15.05 for building mqtt-sn-gateway
When you try to build the Dragino LoRa Gateway OpenWRT firmware under newest linux version (e.g. Ubuntu 18.04 in my case) you need to apply some changes.

The following files need to be adapted.
	
	openwrt-cc-15.05/openwrt/include/prereq-build.mk
	openwrt-cc-15.05/openwrt/build_dir/host/automake-1.15/bin/automake.in
	openwrt-cc-15.05/openwrt/build_dir/host/automake-1.15/bin/automake.tmp

## Appearing failures:

### 1th error during ./set_up_build_environment.sh

	*** Run make defconfig to set up initial .config file (see ./defconfig.log)
	make: *** [staging_dir/host/.prereq-build] Error 1
	cp: Aufruf von stat für '.config' nicht möglich: Datei oder Verzeichnis nicht gefunden
	
Looking into openwrt/defconfig.log
	cat openwrt/defconfig.log
	(...)
	Build dependency: Please install Git (git-core) >= 1.6.5
	(...) 

### 2th error during ./build_image.sh

	make[1]: Leaving directory '/home/bele/git/openwrt-cc-15.05/openwrt'
	Build failed - please re-run with -j1 to see the real error message
	/home/bele/git/openwrt-cc-15.05/openwrt/include/toplevel.mk:181: recipe for target 'world' failed
	make: *** [world] Error 1
	

Rerrun with -s flag

	./build_image.sh -s
	(...)
	Unescaped left brace in regex is illegal here in regex; marked by <-- HERE in m/\${ <-- HERE ([^ \t=:+{}]+)}/ at ./bin/automake.tmp line 3938.
	Makefile:50: recipe for target '/home/bele/git/openwrt-cc-15.05/openwrt/build_dir/host/automake-1.15/.configured' failed
	make[3]: *** [/home/bele/git/openwrt-cc-15.05/openwrt/build_dir/host/automake-1.15/.configured] Error 255
	(...)

# fixes in detail

### 1th error 
the error is in file openwrt-cc-15.05/openwrt/include/prereq-build.mk

	diff --git a/openwrt/include/prereq-build.mk b/openwrt/include/prereq-build.mk
	--- a/openwrt/include/prereq-build.mk
	+++ b/openwrt/include/prereq-build.mk
	@@ -145,7 +145,7 @@ $(eval $(call SetupHostCommand,svn,Please install the 	Subversion client, \
	        svn --version | grep Subversion))
	 
	 $(eval $(call SetupHostCommand,git,Please install Git (git-core) >= 1.6.5, \
	-       git clone 2>&1 | grep -- --recursive))
	+       git --exec-path | xargs -I % -- grep -q -- --recursive %/git-submodule))
	 
	 $(eval $(call SetupHostCommand,file,Please install the 'file' package, \
	        file --version 2>&1 | grep file))


### openwrt/build_dir/host/automake-1.15/bin/automake.in

--- a/bin/automake.in
+++ b/bin/automake.in
@@ -3883,7 +3883,7 @@ sub substitute_ac_subst_variables_worker
 sub substitute_ac_subst_variables
 {
   my ($text) = @_;
-  $text =~ s/\${([^ \t=:+{}]+)}/substitute_ac_subst_variables_worker ($1)/ge;
+  $text =~ s/\$[{]([^ \t=:+{}]+)}/substitute_ac_subst_variables_worker ($1)/ge;
   return $text;
 }

### openwrt/build_dir/host/automake-1.15/bin/automake.tmp

--- a/bin/automake.tmp
+++ b/bin/automake.tmp
@@ -3938,7 +3938,7 @@ sub substitute_ac_subst_variables_worker
 sub substitute_ac_subst_variables
 {
   my ($text) = @_;
-  $text =~ s/\${([^ \t=:+{}]+)}/substitute_ac_subst_variables_worker ($1)/ge;
+  $text =~ s/\$[{]([^ \t=:+{}]+)}/substitute_ac_subst_variables_worker ($1)/ge;
   return $text;
 }

### commands to build
We a assume you habe the openwrt-mqtt-sn-gateway repository next to the current shell directory:
	
	ls -l
	  4 drwxr-xr-x  5 user user   4096 Okt  6 14:22 openwrt-mqtt-sn-gateway
	
Then you can build the openwrt-cc-15.05 gateway and toolchain:

	# libssl1.0-dev needs to be installed, newer version generate an error
	sudo apt install libssl1.0-dev -y
	
	git clone https://github.com/dragino/openwrt-cc-15.05.git openwrt-cc-15.05
	cd openwrt-cc-15.05
	# fix 1th error
	cp ../openwrt-mqtt-sn-gateway/openwrt-cc-15.05/prereq-build.mk openwrt/include/prereq-build.mk
	./set_up_build_environment.sh
	# build once so that automake.in and automake.tmp are created
	./build_image.sh
	# fix 2th error
	cp ../openwrt-mqtt-sn-gateway/openwrt-cc-15.05/automake.in openwrt/build_dir/host/automake-1.15/bin/automake.in
	cp ../openwrt-mqtt-sn-gateway/openwrt-cc-15.05/automake.tmp openwrt/build_dir/host/automake-1.15/bin/automake.tmp
	./build_image.sh
	# get a coffee... or two this takes some hours

Afterwards build the mqtt-sn-gateway like any other feed.
