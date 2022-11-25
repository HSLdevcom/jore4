# Onboarding

This is the starting page for new team members.

## Access to services

You need these accounts:

1. Member access to [HSL Slack](https://hsldevcom.slack.com).
   - Use your preferred email address.
   - Ask the Product Owner (PO).
   - Needed for discussions across the HSL organization. Jore4 relates to many projects.
1. External ("EXT") HSL Microsoft account.
   - Ask the PO:
     - for the Microsoft account
     - to be added to the AD and Azure group `jore4-platform-developers`
     - to be added to the group `jore` in [gitlab.hsl.fi](https://gitlab.hsl.fi)
   - Needed for Azure, [gitlab.hsl.fi](https://gitlab.hsl.fi), Sharepoint and possibly other services where `@hsl.fi` email address gets a preference.
   - Set up Multi-Factor Authentication (MFA). MFA is mandatory for all users and you should be asked to set it up when logging in for the first time.
1. HSL ID account
   - Have a team member create a test account in the HSL ID test environment (https://hslid-dev.t5.fi)
   - In case this does not work or the created account is not visible, contact HSL-ID-people on the appropriate channel in HSL's slack
   - Assign the created account the appropriate roles ("Jore4 admin" at the time of writing)
1. GitHub account
   - If you do not have one, create it yourself: <https://github.com/join>
   - Use your preferred email address.
   - Ask the PO to invite you as a member of the organization [HSLdevcom](https://github.com/HSLdevcom/) and the team [jore4](https://github.com/orgs/HSLdevcom/teams/jore4).
   - Allow [Reviewable](https://reviewable.io/) to access your GitHub account and the repositories of `jore4` that you use by signing in with your GitHub account and authorizing each repository separately.
1. Figma account
   - Use your preferred email address.
   - Ask the PO to add you into the group Jore 4.0 in the organization HSL.

### Connecting to the Azure environment via bastion host

You can access the Azure environment networks using the environment's bastion host via SSH.

#### Creating an SSH key

Use the bash script below to create a new signed ssh key for yourself:

```sh
#!/usr/bin/env bash

set -euo pipefail

# Fill in your own data here:
YOUR_EMAIL_ADDRESS=""
YOUR_FULL_NAME=""

# Making sure that the CA key is deleted
cleanup() {
  echo "Cleaning up"
  rm ~/.ssh/jore4_ca_ed25519
}
trap cleanup EXIT

# Create new user-specific ssh key for yourself
ssh-keygen -t ed25519 -f ~/.ssh/jore4_key_ed25519 -C "${YOUR_EMAIL_ADDRESS}"

# Fetch CA key from Azure
az login
az keyvault secret show \
  --vault-name 'hsl-jore4-vault' \
  --name 'jore4-developer-ca-key-private' \
  --output tsv \
  --query value \
  > ~/.ssh/jore4_ca_ed25519
chmod 0600 ~/.ssh/jore4_ca_ed25519

# Sign the user-specific ssh key with the CA key
ssh-keygen \
  -s ~/.ssh/jore4_ca_ed25519 \
  -I "${YOUR_FULL_NAME} user key" \
  -n 'hsladmin' \
  -V '-5m:+100d' \
  ~/.ssh/jore4_key_ed25519
```

The command above refers to your private key but actually uses your public key with the conventional `.pub` extension. If you modify the command above, do not persist the SSH CA private key on your computer.

Check the [original HSL instructions](https://gitlab.hsl.fi/developer-resources/azure-ansible#creating-user-key-each-user-should-have-their-own) for updates in case you run into trouble.

#### Adding your IP to the whitelist (NSG)

Next, you may have to add your personal IP (as seen by the bastion host) to the appropriate network security group (NSG)
in Azure. Note that this is not needed if you use the bastion host via a VPN whose IP is already
present in the NSG.

Currently, the easiest way to add your address to the NSG is by using the [Azure portal](https://portal.azure.com). Navigate
to the NSG of the public subnet of the environment in question, i.e. find `hsl-jore4-dev-nsg-public` in the list of
resources. (Replace `dev` with `playg`, `test`, or `prod` for the other environments.) Select the NSG by clicking on it and
open the `allow_SSH` inbound security rule. In the `Source IP addresses`-field, add your own IP or range to the
comma-separated list. By saving the changes, you'll be granted access to the bastion host from the IP added.

#### Creating an SSH configuration entry

After these steps, you can add the following snippet into your SSH configuration for the dev environment (the tunnelling part works on Linux but maybe not on Mac):

```ssh-config
Host hsl-jore4-dev-bastion
  HostName bastion.dev.jore.hsl.fi
  User hsladmin
  IdentityFile ~/.ssh/jore4_key_ed25519
  # HSL Jore3 test database
  LocalForward localhost:56239 10.218.6.14:56239
  LocalForward localhost:9432 hsl-jore4-dev-db.postgres.database.azure.com:5432
  ExitOnForwardFailure yes
  ServerAliveInterval 60
```

This will configure ssh to use the appropriate key when accessing the bastion host and to forward both the Jore3 and Jore4 DB connections to your local machine. Additionally, you can create similar configurations for different environments by using a different local port for forwarding and the correct bastion host DNS name:
- `bastion.dev.jore.hsl.fi` - dev environment (as used above)
- `bastion.playg.jore.hsl.fi` - devops playground environment
- `bastion.test.jore.hsl.fi` - test environment
- `bastion.jore.hsl.fi` - prod environment

After performing these steps, you should be able to SSH into the bastion host using the command

```sh
ssh hsl-jore4-dev-bastion
```

In order to prevent the SSH connection to be closed due to inactivity (while using port forwarding in the background), run the following command in the remote shell:

```env TMOUT=0 bash
```

### Jore3 test database

[Jore3](jore3.md) has a test database.

After successfully connecting to the bastion host as described in the section [on how to connect to the Azure environment](#connecting-to-the-azure-environment-via-bastion-host), you can run locally e.g.

```sh
python -m pip install mssql-cli
mssql-cli -S localhost,<LOCAL_PORT> -d joretest
```

to connect to the database.
Use the relevant credentials from the common Azure key vault.
Use care when touching the test database.

Type:

```tsql
\ld
```

to list the available databases in order to test database access and

```tsql
\q
```

to quit.

More information on connecting to the Jore3 test DB and troubleshooting connection problems, see the [Jore3](jore3.md) guide.

## First immersion

Before using Azure at HSL, [read the guide](https://portal.azure.com/#@hslfi.onmicrosoft.com/dashboard/arm/subscriptions/b13714ed-2c1b-416c-89a9-909524515193/resourcegroups/dashboards/providers/microsoft.portal/dashboards/bcea8162-492c-4428-ba8c-19321eceb0cd).

## Improvements

It's your job to update this document to fill gaps and add information you wish you had known.
