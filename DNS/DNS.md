# DNS Server (Bind) Configuration Guide

Ensure your Bind DNS server is installed and running correctly. If not, follow these installation and startup commands:

```bash
sudo apt-get update
sudo apt-get install bind9
sudo systemctl start bind9
sudo systemctl enable bind9
cd /etc/bind
```

Next, check the forwarders in your configuration:

```bash
sudo nano named.conf.options
```

Your forwarders should look something like this:

```bash
forwarders {
     192.168.56.102;
     8.8.8.8;
};
```

Now, configure your domain and reverse zones:

```bash
sudo nano named.conf.local 
```

Your domain and reverse zones might look like this:

```bash
zone "insat.tn" {
    type master;
    file "/etc/bind/db.insat.tn";
};

zone "56.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.reverse.tn";
};
```

You'll also need to add the necessary DNS records for OpenLDAP, Apache, and OpenVPN servers. Begin by generating the `db.insat.tn` file from `db.local`:

```bash
sudo cp db.local db.insat.tn
```

Then, edit the `db.insat.tn` file:

```bash
sudo nano db.insat.tn 
```

Configuration should somewhat look like this:

```bash
...
server  IN A    192.168.56.102
apache  IN A    192.168.56.102
```

Update the `db.reverse.tn` file like how you updated the `db.insat.tn`:

```bash
sudo nano db.reverse.tn
```

Modify the `/etc/resolv.conf`:

```bash
sudo nano /etc/resolv.conf
```

In this file, add your nameserver and domain:

```bash
nameserver 192.168.56.102
domain insat.tn
search insat.tn
```

## Section 2: Validation and Testing

Perform a DNS resolution test for your configured services. Ensure the DNS server is running:

```bash
sudo systemctl restart bind9
sudo systemctl status bind9
```

Use the `nslookup` command to test DNS resolution:

```bash
nslookup server.insat.tn
nslookup apache.insat.tn
```
