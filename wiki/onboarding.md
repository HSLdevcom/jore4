# Onboarding

This is the starting page for new team members.

## Access to services

You need these accounts:

1. Member access to [HSL Slack](https://hsldevcom.slack.com).
   - Use your preferred email address.
   - Ask the Product Owner (PO).
   - Needed for discussions across the HSL organization. Jore4 relates to many projects.
1. External ("EXT") HSL Microsoft account.
   - Ask the PO.
   - Needed for Azure, gitlab.hsl.fi, Sharepoint and possibly other services where `@hsl.fi` email address gets a preference.
1. HSL ID account
   - Create it yourself: <https://www.hsl.fi/user/auth/login?language=fi>
   - There are also test versions for HSL ID.
   - Use your preferred email address unless told otherwise.
   - Ask the PO to add your email address into Jore3 so that you can sign into Jore Map UI.
1. GitHub account
   - If you do not have one, create it yourself: <https://github.com/join>
   - Use your preferred email address.
   - Ask the PO to invite you as a member of the organization [HSLdevcom](https://github.com/HSLdevcom/) and the team [jore4](https://github.com/orgs/HSLdevcom/teams/jore4).
   - Allow [Reviewable](https://reviewable.io/) to access your GitHub account and the repositories of `jore4` that you use by signing in with your GitHub account and authorizing each repository separately.
1. Figma account

### Connecting to the Azure environment via bastion host

You can access the Azure environment networks using the environment's bastion host via SSH.

To connect to it, you need to
- create a new key pair (not strictly needed, but recommended as best practice), e.g. using the command
  ```
  ssh-keygen -t ed25519 -f ~/.ssh/jore4_key_ed25519 -C "<YOUR_EMAIL_ADDRESS>"
  ```
- download the CA private key, e.g. using the commands
  ```
  az login
  mkdir -p /tmp/jore
  az keyvault secret show --vault-name hsl-jore4-vault --name jore4-developer-ca-key-private --output tsv --query value > /tmp/jore/jore4.key
  chmod 0600 /tmp/jore/jore4.key
  ```
- sign your own key pair with the private CA key using the command
  ```
  ssh-keygen -s /tmp/jore/jore4.key -I <YOUR_USER_NAME> -n hsladmin -V -5m:+100d ~/.ssh/jore4_key_ed25519
  ```
- REMOVE THE PRIVATE KEY from your machine:
  ```
  rm /tmp/jore/jore4.key
  ```

Ask the team about the CA key details. Check the [original HSL instructions](https://gitlab.hsl.fi/developer-resources/azure-ansible#creating-user-key-each-user-should-have-their-own) for updates in case you run into trouble.

Once you have signed your SSH key, you can add the following snippet into your SSH configuration (works on Linux, maybe not on Mac):

```ssh-config
# Add into ~/.ssh/config after filling out <VARIABLE>s

Host hsl-jore4-dev-bastion
  HostName <BASTION_HOST_IP>
  User hsladmin
  IdentityFile ~/.ssh/jore4_key_ed25519
  # HSL Jore3 test database
  LocalForward localhost:<LOCAL_PORT> 10.218.6.14:56239
  ExitOnForwardFailure yes
```

This will configure ssh to use the appropriate key when accessing the bastion host and to forward the Jore3 DB connection to your local machine. Ask the sensitive details from the team.

After performing these steps, you should be able to SSH into the bastion host using the command
```
ssh hsladmin@hsl-jore4-dev-bastion
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
