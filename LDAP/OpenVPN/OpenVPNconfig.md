# Open VPN Setup

This project involves the implementation of an Open VPN system utilizing OpenLDAP identifiers. 

## Installation Process

#### OpenVPN Server Setup:

1. Install OpenVPN and configure it for OpenLDAP identification.

 - `sudo apt-get install openvpn easy-rsa`

 - `sudo apt-get install openvpn-auth-ldap`

- We will need to modify the /etc/openvpn/auth/ldap.conf file to specify how OpenVPN should interact with the OpenLDAP server.

#### Firewall Configuration:

- Open port 1194 to ensure VPN traffic can traverse through it. 

- OpenLDAP based authentication configuration mainly exists in `server.conf` where the authorization policy is defined. 

- Here, we are only allowing users who belong to the group `GL` to connect with the VPN. 

- For this, we used the command: 

``` 
< Group>
BaseDN "ou-groups, dc-insat,dc=tn"
SearchFilter "(&(objectClass-posixGroup)(cn=GL))*
MemberAttribute memberUid
</ Group>
```

## VPN Testing Process
#### Virtual Machine Settings:

- For this project, we are using four virtual machines (VM), namely the Certification Authority (CA) server, OpenVPN server, and two clients - Emna and Hamza.

#### Certification Authority Server Setup:

- Create the Certification Authority (CA) server using the command - `#easyrsa build-ca` 

- Some of the server settings need to be configured such as - `easyrsa_req_country`.

#### OpenVPN Server Certificate Generation:

- Generate a request on the OpenVPN server using the command - `#easyrsa gen-req server nopass`

- The request is imported and signed by the CA server using the commands - 

```
#easyrsa import-req /tmp/server.req server
#easyrsa sign-req server server
```

- Copy the signed certificates and CA's certificate to the OpenVPN server via scp command. 

```
# scp pki/issued/server.crt kali@192.168.56.101:/tmp/
# cp pki/ca.crt kali@192.168.56.101:/tmp//tmp/
```

#### Client Certificate Generation:

- Generate certificate requests for the client systems - Emna and Hamza using easyrsa - 

```
#easyrsa gen-req emna nopass
#easyrsa gen-req hamza nopass
```

- These requests are then imported and signed by the CA server with the client type.

## VPN Connection test with OpenLDAP credentials:

#### Allowed and Not Allowed client VPN tunneling:

- Test candidates: Emna (Not part of GL group and hence not allowed) and Hamza (Part of GL group and hence allowed)

- Final test reveals Hamza being able to connect to the VPN server, but Emna, who is not part of the GL group, could not.
