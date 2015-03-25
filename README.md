Syslog
=====================
Overview
--------------------
this is a syslog over tls setup intended for enviorments where you need syslog-ng for the main server but have to forward logs from older centos 5/6 machines to it. The setup has a central syslog-ng server on centos 7 which supports syslog-ng tls and then rsyslog client servers that forward logs. If you do not have a bunch of older systems you are probably better of with a setup that uses the same syslog service on every thing. 

Files
----------------------
- /etc/syslog-ng/               || base config directory
- /etc/syslog-ng/syslog-ng.conf || default config file
- /etc/syslog-ng/db-insert.php  || custom db insert script used for 
- /etc/syslog-ng/ca.d/          || stores ca certs has file and a generated number that links to the file
- /etc/syslog-ng/cert.d/        || stores certs for tls encryption
- /etc/syslog-ng/conf.d/        || stores extra configs. I try and keep custom stuff in here
- /var/log/remotelogs/          || logs for specific servers logging here

Requirements
----------------------
- server assigened to syslogng group and ca groun in ansible_hosts. They can be the same server. Run the playbook against them first.
- Centos 7 || for the syslog-ng server. lower versions of centos have bugs that prevent tls working for syslog-ng
- Centos 5,6,7 || these will all work for forwarding log files to the main server
- open ports: tcp in/out port 514 || tls traffic from all our servers logging here
- open ports:  tcp in/out port 22 || for file transfer of ca certs

CA
=========================
Overview
------------------------
The stuff for setting up a CA could be its own role but for now its part of the syslog role. You are going to need a server in the ca group. You are also going to need to generate a id_rsa key and add it to this role as well as put the public key for it in the capublickey variable
  
Ansible Variables
------------------------
- countryName_default=US
- stateOrProvinceName_default=Arizona
- localityName_default=Phoenix 
- organizationalUnitName_default=CompanyName

Files
------------------------
- /home/ca/CA/cacert.pem        || ca cert this can be freely distrubted and is on all of our servers that are doing syslog over tls
- /home/ca/CA/private/cakey.pem || this is the ca private key. if we lose this any one can sign certs as us
- /home/ca/CA/private/          || contains all private keys for our servers that have been generated
- /home/ca/CA/newcerts          || contains all of the certs we have generated
- /home/ca/CA/openssl.conf      || specific configuration options for generating certs. this is custom
- /home/ca/CA/index*            || lots of crap like index* serial ect. this is used to keep track of certs we have already signed
