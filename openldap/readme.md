# Nutanix Calm & OpenLDAP

This Nutanix Calm blueprint will deploy OpenLDAP in preparation for use with a Nutanix cluster.

The intention of this blueprint is to allow customers testing of OpenLDAP, a common alternative to Active Directory.

## Components

- 1x OpenLDAP server
- 1x CentOS 7 Linux VM configured to authenticate against the new OpenLDAP directory

*Note: ntnxdemo.local is hard-coded as the domain, for now*

## Prerequisites

To deploy this blueprint you will need the following things available.

- A CentOS 7 Linux VM image, published via the Nutanix Image Service
- Username and password for your Linux image (note that this blueprint does not natively use of public/private key authentication)

## Usage

- Import the blueprint into your Nutanix Calm environment
- Adjust credentials to suit your requirements (the import will warn about blueprint validation errors, since credentials are not saved in exported blueprints)
- Alter each Calm service/VM so that it deploys your CentOS 7 image
- Alter each Calm service/VM so that it connects to your preferred network
- Launch the blueprint
- Fill in all required runtime variables, paying particular attention to the credentials section
- After deployment, connect to the deployed Linux client and login with the credentials supplied during deployment
- To use phpLDAPAdmin, browse to `http://<openldap_server_ip_address>/phpldapadmin (login using the OPENLDAP_PASSWORD entered during blueprint launch)
- Configure Prism and/or Prism Central Authentication, as per the next section

## Nutanix Authentication

To setup Nutanix Prism or Prism Central to authenticate via OpenLDAP, follow the steps below.

### Directory

- From Prism or Prism Central, click the gear icon and select `Authentication`
- Select `New Directory` and set the type to `OpenLDAP`

For each field, use settings similar to the following:

- Name: The display name for the OpenLDAP directory
- Domain: The domain name, `ntnxdemo.local` in this case
- Directory URL: `ldap://<openldap_server_ip_address>:389`
- User Object Class: `account`
- User Search Base: `ou=People,dc=ntnxdemo,dc=local`
- Username Attribute: `uid`
- Group Object Class: `posixgroup`
- Group Search Base: `ou=Group,dc=ntnxdemo,dc=local`
- Group Member Attribute: `memberUid`
- Group Member Attribute Value: `uid`
- Service Account Username: `cn=ldapadm,dc=ntnxdemo,dc=local`
- Service Account Password: `<OPENLDAP_PASSWORD>` as entered during blueprint launch

### Roles

- From Prism or Prism Central, click the gear icon and select Role Mapping
- Click `New Mapping`
- Select your new directory from the Directory dropdown
- Select the type of authentication (for this environment it is recommended to select `group`)
- Select the role (for this environment it is recommended to select `Cluster Admin` the first time)
- Enter the OpenLDAP group name to associate with this role (in this blueprint, preconfigured groups called `ClusterAdmin` and `Viewer` are available)
- Repeat the Roles steps above to create a role for the unused group
