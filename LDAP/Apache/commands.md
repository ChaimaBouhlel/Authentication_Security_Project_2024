# README

This tutorial shows you how to integrate Apache with OpenLDAP authentication. You will install required packages, activate Apache modules, edit Apache configuration, limit web page access to certain users, and test user access.

## Step 1: Install Packages

To start, we need to install the necessary packages for Apache and PHP. Run the following command:

```bash
sudo apt install apache2 php php-cgi libapache2-mod-php php-mbstring php-common php-pear -y
```

## Step 2: Enable Apache Modules

Next, we have to activate Apache modules that are necessary for authentication with an OpenLDAP server. Run:

```bash
a2enmod ldap authnz_ldap
```

## Step 3: Edit Apache Configuration to limit Web Page Access

Now, we specify how Apache should handle LDAP authentication for content in specified directory.

```bash
sudo nano /etc/apache2/sites-available/auth-ldap.conf 
```

With the configuration block, you can restrict access to /var/www/html/auth-ldap directory to users who are members of the RT group in the specified LDAP directory.

```markdown
<Directory /var/www/html/auth-ldap>
    AuthName "ldap auth"
    AuthType Basic
    AuthBasicProvider ldap
    AuthLDAPURL ldap://localhost/dc=insat,dc=tn?uid?sub?(objectClass=*)
    AuthLDAPGroupAttribute memberUid
    AuthLDAPGroupAttributeIsDN off
    AuthLDAPBindDN "cn=admin,dc=insat,dc=tn"
    AuthLDAPBindPassword "admin"
    Require ldap-filter (memberOf=cn=RT,ou=groups,dc=insat,dc=tn)
    Require ldap-group cn=RT,ou=groups,dc=insat,dc=tn
</Directory>
```

## Step 4: Activate Configuration and Restart Apache

After editing, you must activate the configuration by creating a symbolic link to the /etc/apache2/sites-enabled/ directory. Also, restart the Apache service for changes to reflect:

```bash
sudo a2ensite auth-ldap.conf
sudo service apache2 restart
```

## Step 5: Test Page

Create an ```index.html``` file in the ```/var/www/html/auth-ldap/``` directory for testing the LDAP authentication with Apache:

```bash
sudo nano /var/www/html/auth-ldap/index.html
```

## Step 6: User Access Tests

That's all you need to set up Apache with OpenLDAP authentication. It ensures only authorized users have access to certain content directories.
