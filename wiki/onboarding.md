# Onboarding

This is the starting page for new team members.

## Access to services

You need these accounts:
1. A member access to [HSL Slack](https://hsldevcom.slack.com).
    - Ask the Product Owner (PO).
    - Use your preferred email address.
    - Needed for discussions across the HSL organization. Jore4 relates to many projects.
1. An external ("EXT") HSL Microsoft account.
    - Ask the PO.
    - Needed for Azure, gitlab.hsl.fi, Sharepoint and possibly other services where `@hsl.fi` email address gets a preference.
1. HSL ID account
    - Create it yourself: <https://www.hsl.fi/user/auth/login?language=fi>
    - There are also test versions for HSL ID. 
    - Use your preferred email address unless told otherwise.
    - Ask the PO to add your email address into Jore3 so that you can sign into Jore Map UI.

Also create [a project-specific SSH key](https://gitlab.hsl.fi/developer-resources/azure-ansible#creating-user-key-each-user-should-have-their-own).

### Connecting to the Azure environment via bastion host

You can access the Azure environment networks using the environment's bastion host via SSH.

To connect to it, you need to [sign your SSH key](https://gitlab.hsl.fi/developer-resources/azure-ansible#signing-users-public-key-with-private-ca-key).
Ask the team how to access the CA key.

This should allow you to ssh into the bastion host only using your public key. You should be able to forward the Jore3 test DB connection to your own machine using e.g. using the command `ssh -L 15432:10.218.6.14:56239 hsladmin@<BASTION_HOST_IP>`.

In case you have trouble using your signed public key, you may also use the private key directly. In order to do this, you can add this into your SSH configuration (works on Linux, maybe not on Mac):
```ssh-config
# Add into ~/.ssh/config after filling out <VARIABLE>s

Host hsl-jore4-dev-bastion
  HostName <BASTION_HOST_IP>
  User <USER_NAME>
  IdentityFile ~/.ssh/jore4_key_ed25519
  # HSL Jore3 test database
  LocalForward localhost:<LOCAL_PORT> <JORE3_TEST_DB_IP>:<JORE3_TEST_DB_PORT>
  ExitOnForwardFailure yes
```

Ask the sensitive details from the team.

### Jore3 test database

[Jore3](jore3.md) has a test database.

After successfully connecting to the bastion host as described in the section [on how to connect to the Azure environment](### Connecting to the Azure environment via bastion host), you can run locally e.g. 
```sh
python -m pip install mssql-cli
mssql-cli -S localhost,<LOCAL_PORT> -d joretest
```
to connect to the database.
Use the relevant credentials from the common Azure key vault.
Use care when touching the test database.

Type:
```tsql
\q
```
to quit.

More information on connecting to the Jore3 test DB and troubleshooting connection problems, see the [Jore3](jore3.md) guide.

## First immersion

Before using Azure at HSL, [read the guide](https://portal.azure.com/#@hslfi.onmicrosoft.com/dashboard/arm/subscriptions/b13714ed-2c1b-416c-89a9-909524515193/resourcegroups/dashboards/providers/microsoft.portal/dashboards/bcea8162-492c-4428-ba8c-19321eceb0cd).
