# SWUpdate
Tested in a Debian 11:

Download swupdate from oficial repository and compile:

```
cd /usr/local/src
git clone https://github.com/sbabic/swupdate.git  && cd swupdate
make test_defconfig
make menuconfig
apt-get install lua5.2-dev libssl-dev libconfig-dev libarchive-dev libzmq3-dev libz-dev libcurl4-gnutls-dev libjson-c-dev build-essential
make && make install
```

Create the certificates:
```
openssl genrsa -out swupdate-priv.pem
openssl rsa -in swupdate-priv.pem -out swupdate-public.pem -outform PEM -pubout
```

Clone this repository and access to folder:
```
git clone https://github.com/danielcastropalomares/SWUpdate.git
cd SWUpdate
```
Create a signature for sw-description
```
openssl dgst -sha256 -sign /usr/local/src/swupdate/swupdate-priv.pem sw-description > sw-description.sig
```

Create the file .swu
```
 export FILES="sw-description sw-description.sig SWUpdate"
 for i in $FILES; do echo $i; done | cpio -ov -H crc >  update-image-v1.swu
```

# Install SWU package
In target machine, repeat the process of the install swupdate.

Now need create a file to specify the model. With this file, the swupdate check the package version of the .swu and the system:
```
cat /etc/hwrevision
danimachine 1.0
```
Install the swu package via CLI:

```
 swupdate -v -k /usr/local/src/swupdate/swupdate-public.pem -i update-image-v1.swu 
```

Optional: In other way, you can start a webserver to upload the firmware via web:
```
swupdate -v -k /usr/local/src/swupdate/swupdate-public.pem -w '-r /usr/local/src/swupdate/examples/www/v2 -p 8081' -p 'echo reboot'
```


The output:

```
root@debian:~/update-image# swupdate -v -k /usr/local/src/swupdate/swupdate-public.pem -i update-image-v1.swu
Swupdate v2021.11.0

Licensed under GPLv2. See source distribution for detailed copyright notices.

[INFO ] : SWUPDATE running :  [lua_handlers_init] : No Lua handler(s) found.
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     /usr/local/share/lua/5.2/swupdate_handlers.lua
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     /usr/local/share/lua/5.2/swupdate_handlers/init.lua
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     /usr/local/lib/lua/5.2/swupdate_handlers.lua
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     /usr/local/lib/lua/5.2/swupdate_handlers/init.lua
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     /usr/share/lua/5.2/swupdate_handlers.lua
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     /usr/share/lua/5.2/swupdate_handlers/init.lua
[TRACE] : SWUPDATE running :  [lua_handlers_init] :     ./swupdate_handlers.lua
[INFO ] : SWUPDATE running :  [main] : Running on danimachine Revision 1.0
[INFO ] : SWUPDATE running :  [print_registered_handlers] : Registered handlers:
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     dummy
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     archive
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     tar
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     lua
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     raw
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     rawfile
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     rawcopy
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     remote
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     shellscript
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     preinstall
[INFO ] : SWUPDATE running :  [print_registered_handlers] :     postinstall
[TRACE] : SWUPDATE running :  [listener_create] : creating socket at /tmp/swupdateprog
[TRACE] : SWUPDATE running :  [network_initializer] : Main loop daemon
[TRACE] : SWUPDATE running :  [listener_create] : creating socket at /tmp/sockinstctrl
[TRACE] : SWUPDATE running :  [network_thread] : Incoming network request: processing...
[INFO ] : SWUPDATE started :  Software Update started !
[TRACE] : SWUPDATE running :  [network_initializer] : Software update started
[TRACE] : SWUPDATE running :  [extract_file_to_tmp] : Found file
[TRACE] : SWUPDATE running :  [extract_file_to_tmp] :   filename sw-description
[TRACE] : SWUPDATE running :  [extract_file_to_tmp] :   size 321
[TRACE] : SWUPDATE running :  [extract_file_to_tmp] : Found file
[TRACE] : SWUPDATE running :  [extract_file_to_tmp] :   filename sw-description.sig
[TRACE] : SWUPDATE running :  [extract_file_to_tmp] :   size 256
[TRACE] : SWUPDATE running :  [swupdate_verify_file] : Verify signed image: Read 321 bytes
[TRACE] : SWUPDATE running :  [swupdate_verify_file] : Verified OK
[TRACE] : SWUPDATE running :  [get_common_fields] : Version 1.0.1
[TRACE] : SWUPDATE running :  [parse_hw_compatibility] : Accepted Hw Revision : 1.0
[TRACE] : SWUPDATE running :  [_parse_files] : Found File: SWUpdate --> /SWUpdate (ROOTFS)
[TRACE] : SWUPDATE running :  [check_hw_compatibility] : Hardware danimachine Revision: 1.0
[TRACE] : SWUPDATE running :  [check_hw_compatibility] : Hardware compatibility verified
[TRACE] : SWUPDATE running :  [extract_files] : Found file
[TRACE] : SWUPDATE running :  [extract_files] :         filename SWUpdate
[TRACE] : SWUPDATE running :  [extract_files] :         size 12 required
[TRACE] : SWUPDATE running :  [extract_padding] : Expecting 376 padding bytes at end-of-file
[TRACE] : SWUPDATE running :  [network_initializer] : Valid image found: copying to FLASH
[INFO ] : SWUPDATE running :  Installation in progress
[TRACE] : SWUPDATE running :  [install_single_image] : Found installer for stream SWUpdate rawfile
[TRACE] : SWUPDATE running :  [install_raw_file] : Installing file SWUpdate on /SWUpdate
[INFO ] : SWUPDATE successful ! SWUPDATE successful !
[TRACE] : SWUPDATE running :  [network_initializer] : Main thread sleep again !
[INFO ] : No SWUPDATE running :  Waiting for requests...
[INFO ] : SWUPDATE running :  [endupdate] : Swupdate was successful !
```

