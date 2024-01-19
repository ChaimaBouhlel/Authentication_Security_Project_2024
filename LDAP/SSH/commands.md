# SSH Authentication README

This guide explains how to enable SSH Authentication via OpenLDAP, restrict SSH access to specified OpenLDAP groups and how to test access for authorized and unauthorized users.

- **Step 1:** Installation of necessary packages with the following command:
  
  `sudo apt-get install openssh-server libpam-ldap libnss-ldap`
  
  - `openssh-server`: Installs the SSH server to enable remote connections.
  - `libpam-ldap`: PAM library for LDAP support, which allows OpenSSH to use LDAP for authentication.
  - `libnss-ldap`: NSS library for name resolution via LDAP, which allows OpenSSH to fetch user and group information from LDAP.

- **Step 2:** Configuration of /etc/nsswitch.conf file to indicate how the system should resolve user and group names.

  `sudo nano /etc/nsswitch.conf`
  
  Add `compat ldap` which indicates that user, group and shadow information are obtained from local files (/etc/passwd, /etc/group) and LDAP.

- **Step 3:** Restrict SSH access to authorized groups in OpenLDAP by modifying the configuration file /etc/ssh/sshd_config:

  `sudo nano /etc/ssh/sshd_config`
  
  Uncomment or add these lines:

  - `PubkeyAuthentication yes`: Enables public key authentication.
  - `ChallengeResponseAuthentication yes`: Enables challenge-response authentication, which may include methods such as PAM authentication.
  - `PasswordAuthentication yes`: Enables password authentication.
  - `KbdInteractiveAuthentication no`: Disables keyboard interactive authentication.

- **Step 4:** Edit access control configuration file /etc/security/access.conf:
  
  `sudo nano /etc/security/access.conf`

- **Step 5:** Modify PAM SSH configuration file /etc/pam.d/sshd:

  `sudo nano /etc/pam.d/sshd`

  Add the following line at the top of the file to enable LDAP authentication:

  `auth required pam_ldap.so`
  
- **Step 6:** Apply configuration changes by restarting services like NSCD, NSLCD and SSH:

  ```
  sudo systemctl restart nscd
  sudo systemctl restart nslcd
  sudo systemctl restart ssh
  ```

- **Step 7:** Test for authorized and unauthorized users with the ssh command:

  `ssh username@hostname`
  
  Replace `username` with the user under test and `hostname` with your target server's hostname.

If done correctly, authorized users should be able to connect while unauthorized users’ authentication fails.
