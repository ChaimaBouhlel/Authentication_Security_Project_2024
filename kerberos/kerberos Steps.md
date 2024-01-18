# Kerberos Implementation - Readme File

This ReadME walks through the process of how to install and configure Kerberos in your system. Kerberos is a security protocol that uses secret-key cryptography for secure communication over a network.

### Initial Configuration
- sudo nano /etc/hosts: Edit your hosts file to map the hostnames to IPs. Nano is a command-line text editor, and sudo ensures you have the necessary permissions.
- getent hosts: Display entries from the current hosts database.
- hostnamectl --static set-hostname kdc.insat.tn: Set your system's hostname to 'kdc.insat.tn'.

### Installing Kerberos
- sudo apt install krb5-kdc krb5-admin-server krb5-config: Install Kerberos packages.

### Kerberos Configuration
- nano /etc/krb5.conf: Edit the Kerberos configuration file.
- sudo krb5_newrealm: Initialize a new Kerberos realm.

To add a principal to the newly created realm, switch to root user:
- sudo su: Switch to root user.
- Navigate to the /etc/krb5kdc directory.

Inside the directory:
- kadmin.local: Start the Kerberos administration server.

### Principal Management
- add_principal utilisateur: Add a new user 'utilisateur'.
- add_principal root/admin: Add 'root' as an administrative principal.
- list_principals: List all the principals.
- get_principal utilisateur: Get the details of specific principal 'utilisateur'.
- add_principal host/kdc.insat.tn: Add a host principal.

To exit administration: q.

### Kerberos Servers
- systemctl restart krb5-kdc: Restart Kerberos key distribution center server.
- systemctl restart krb5-admin-server: Restart Kerberos admin server.

### Keytab Generation
- ktutil: Start the key table utility.
- add_entry -password -p root/admin@INSAT.tn -k 1 -e aes256-cts-hmac-sha1-96: Create a new entry using specified encryption.
- wkt /etc/krb5kdc/kadm5.keytab: Write keytab to file.
- q: Exit ktutil.

### SSH Server Installation
- apt install openssh-server: Install OpenSSH server.

### SSH Configuration
- /etc/ssh/sshd_config: Config file for the SSH daemon.
- /etc/ssh/ssh_config: Config file for the SSH client.

### User Management
- adduser utilisateur: Add a new user 'utilisateur'.
- su -l utilisateur: Switch to the new user.

### Logging In
- kinit: Acquire and cache Kerberos ticket-granting ticket.
- ssh kdc.insat.tn: Connect to 'kdc.insat.tn' via SSH.

For a different machine:
- Create a new user (use commands mentioned above).
- kinit
- ssh kdc.insat.tn: SSH into 'kdc.insat.tn'.