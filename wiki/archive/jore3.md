# Jore3

The mighty predecessor to Jore4 is connected to many systems and affects many business processes.

## Test database

To rotate a password for the jore3-test-db SQL server, connect to the test database and run:
```tsql
ALTER LOGIN user_name WITH PASSWORD = 'new_password' OLD_PASSWORD = 'old_password';
```

`OLD_PASSWORD` is required so that the current user does not need [`ALTER ANY LOGIN` privilege](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-login-transact-sql?view=sql-server-ver15#c-changing-the-password-of-a-login-when-logged-in-as-the-login).

## Jore3 db access

The Jore3 db can be accessed through the environments' bastion host. For information on how to connect to the bastion host, see the [onboarding guide](onboarding.md).

### Troubleshooting Jore3 db access

1. Can you log into the bastion host? E.g. `ssh hsladmin@bastion.dev.jore.hsl.fi`
   If not, check
   - that there is a bastion host in the environment you use and that you are using
   - that you are using the right host name
   - that the bastion host is running, i.e. not in stopped state (in Azure portal)
   - you are using VPN / have been assigned an IP from the allowed range (see vnet network security group)
   - you are using either a signed public key or the private key itself to log in (see 

2. Can you access the db from the bastion host? E.g. run `exec 6<>/dev/tcp/10.218.6.14/56239` on the bastion host.
   If not (exec cannot get connection), check
   - that the -private -subnet of the environment in question uses the "jore4-route"-routing table
   - the bastion host has a route entry routing db traffic to the private subnet gateway (see section below)
   - that the firewall on the Jore3 db side has an exception for the environment in question (contact customer representative)
   - that the peering to the Jore3 network has been established (contact customer representative)

### Checking the bastion host routing table

To check, execute `route -n` on the bastion host. This will return sth like 
```Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.223.29.233   0.0.0.0         UG    100    0        0 eth0
10.223.29.0     0.0.0.0         255.255.255.128 U     0      0        0 eth1
10.223.29.232   0.0.0.0         255.255.255.248 U     0      0        0 eth0
168.63.129.16   10.223.29.233   255.255.255.255 UGH   100    0        0 eth0
169.254.169.254 10.223.29.233   255.255.255.255 UGH   100    0        0 eth0
```

If there is no route matching destination 10.218.6.14 (in the best case 10.218.6.0/27), a route needs to be added. For this, note the IP of the router of the private subnet (the first IP in that network). *This should be the network routed to eth1, but this is not certain. You may want to double check, e.g. from the Azure portal.* In this case the private subnet of the vnet used is 10.223.29.0/25 with the router IP being 10.223.29.1 .

In this case, the missing route can be added using `sudo route add -net 10.218.6.0/27 gw 10.223.29.1`. After that, `route -n` will output the following routing table:
```Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.223.29.233   0.0.0.0         UG    100    0        0 eth0
10.218.6.0      10.223.29.1     255.255.255.224 UG    0      0        0 eth1
10.223.29.0     0.0.0.0         255.255.255.128 U     0      0        0 eth1
10.223.29.232   0.0.0.0         255.255.255.248 U     0      0        0 eth0
168.63.129.16   10.223.29.233   255.255.255.255 UGH   100    0        0 eth0
169.254.169.254 10.223.29.233   255.255.255.255 UGH   100    0        0 eth0
```
The bastion host should now be able to route traffic to the Jore3 db.

In order to persist the change across reboots, you are advised to add the route -command to e.g. `/etc/rc.local` or similar.
