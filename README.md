# SWUpdate
Tested in a Debian 11:
Download swupdate from oficial repository:

cd /usr/local/src
git clone https://github.com/sbabic/swupdate.git  && cd swupdate
make test_defconfig
make menuconfig
apt-get install lua5.2-dev libssl-dev libconfig-dev libarchive-dev libzmq3-dev libz-dev libcurl4-gnutls-dev libjson-c-dev build-essential
make && make install

Create the certificates:

openssl genrsa -out swupdate-priv.pem
openssl rsa -in swupdate-priv.pem -out swupdate-public.pem -outform PEM -pubout

Create the next file in target machine:

cat /etc/hwrevision
danimachine 1.0

Create a signature for sw-description

openssl dgst -sha256 -sign /usr/local/src/swupdate/swupdate-priv.pem sw-description > sw-description.sig

Create the file .swu
 export FILES="sw-description sw-description.sig SWUpdate"
 for i in $FILES; do echo $i; done | cpio -ov -H crc >  update-image-v1.swu

#Install

 swupdate -v -k /usr/local/src/swupdate/swupdate-public.pem -i update-image-v1.swu 
