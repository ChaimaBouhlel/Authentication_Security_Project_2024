# OpenLDAP Configuration Project

This Readme file contains steps and commands to configure OpenLDAP with brief explanations.

## Section 1 : Configuration of OpenLDAP 

### 1.1 Set up an OpenLDAP server with at least two users and two groups

1. **Install OpenLDAP**: 
```
sudo apt install slapd ldap-utils -y
```
2. **Server Configuration**: 

Configure the OpenLDAP server using this command: 
```
sudo dpkg-reconfigure slapd
```
During configuration, you would specify the LDAP database's root domain name (DN). For example, if the domain is "insat.tn", the DN would be "dc=insat, dc=tn". 

3. **Adding Data**: 

To add data to the directory using LDAP protocol use `ldapadd` command. This fetches superuser LDAP credentials, requests password with -W option, then uses the ou.ldif file to add the data to the directory. 

To create an ou.ldif file and execute the command, use:
```
ldapadd -x -D cn=admin,dc=insat,dc=tn -W -f ou.ldif 
```
To add users using the add_user.ldif, and execute the command, use:
```
ldapmodify -x -D cn=admin,dc=insat,dc=tn -W -f add_users.ldif
```

You can use a graphical tool to view the created groups and users.

### 1.2 Add information of your choice including the x509 certificate for all users

You can either use commands or a graphical interface to modify the entries of your LDAP server. To add your authority's certificate, use the ca.crt command.


### 1.3 Ensure that users can successfully authenticate on the OpenLDAP server

1. **Client Machine Configuration**: 

Install necessary LDAP client packages on the client machine, and the server's IP address in the machine's hosts:
```
sudo apt-get install ldap-utils libpam-ldap libnss-ldap
```
2. **Configure /etc/nsswitch.conf**

Modify the /etc/nsswitch.conf configuration file to indicate to the machine how to resolve user and group information.
```
sudo nano /etc/nsswitch.conf
```
3. **Configure /etc/pam.d/common-session**

Set the common session parameters for services using the PAM (Pluggable Authentication Modules) system. Add the following line:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=077
```
Open the settings with:
```
sudo nano /etc/pam.d/common-session
```
4. **Restart services**

After changes in the /etc/pam.d/common-session file, restart the services using PAM for the changes to take effect.
```
sudo systemctl restart nscd
sudo systemctl restart nslcd
```

### 1.4 Test the secure part of ldap with ldaps, and describe the various advantages.

1. **Step 1: Generate Self-Signed SSL Certificate**
```
mkdir ~/ssl-ldap && cd ~/ssl-ldap
openssl genrsa -aes128 -out server.key 4096
openssl rsa -in server.key  -out server.key
openssl req -new -days 3650 -key server.key -out server.csr
sudo openssl x509 -in server.csr -out server.crt -req -signkey server.key -days 3650
```
2. **Step 2: Modify Configuration Files**

Change SLAPD_SERVICES in /etc/default/slapd to: SLAPD_SERVICES="ldap:/// ldapi:/// ldaps:///"
```
sudo nano /etc/default/slapd
```
Modify /etc/ldap/lapd.conf
```
sudo nano /etc/ldap/ldap.conf 
```
by adding the following lines:
```
TLS_CACERT /etc/ldap/sasl2/ca-certificates.crt
TLS REQCERT allow
```
3. **Step 3: Restart Services**
```
sudo systemctl restart slapd
```
4. **Step 4: Test**
```
ldapsearch -x -H ldaps://kali.insat.tn -b "dc=insat,dc=tn"
```
and check the port with:
```
netstat -antup | grep -i 636
```