# Samba 4 Active Directory Domain Controller on RHEL/CentOS7

This project provisions a Samba 4 active directory domain controller on a Vagrant box running RHEL/CentOS7. 

Presently (September 2017) active directory domain controller functionality is not available with the official packages (samba-dc). This project builds Samba 4 from source in order to enable that functionality.

This is not intended for a production environment. I created this primarily for testing purposes and to familiarize myself with Ansible.

If you're here looking for information on integrating active directory with RHEL I recommend you check out the following:

https://www.redhat.com/files/summit/2014/heslin_pal_tue_1540_rhel7-windows_interoperability_update-final1.pdf


## Getting Started

### Prerequisites

I tested this with:

* Ansible 2.3.2.0
* Vagrant 1.9.8
* VirtualBox 5.1.26
* Samba 4.6.7

### Installing and running

1. Download the project:

```
$ git clone git://github.com/windwheel/samba-ad-dc.git
```

2. Edit Vagrantfile to configure the VM name, hostname and IP address.

3. Edit vars.yml to configure default domain administrator password, domain provision options, Samba install path, etc.

4. Run the project:
```
vagrant up
```

## Re-provisioning

WARNING: Re-provisioning will wipe out existing Samba 4 database files! It will create a new domain with a new SID! Be aware of this if you have any clients joined to your Samba 4 AD domain. 

To re-provision:
1. Delete /var/log/.samba_ad_created on the Vagrant box. This file is created and checked by the Ansible playbook. If the file exists (i.e. domain already provisioned) then the playbook will intentionally abort.
2. On the VM host run:
```
vagrant provision
```

## Tests

Successfully joined a Win2012R2 server to the Samba 4 AD domain. Tested basic functionality with Remote Server Administration Tools (RSAT) (creating users, authenticating as created users, etc). Verified NIS extensions (if enabled) with ldbsearch. 

Depending on which Samba features you want to use you may need to muck around with SELinux (`getsebool -a | grep samba`) or use a DNS backend other than SAMBA_INTERNAL. See the official Samba 4 docs for more information: https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller

## Why do the official Samba 4 packages not support AD?

The README from the official samba-dc package explains the lack of active directory domain controller support:

```
MIT Kerberos 5 Support
=======================

Fedora is using MIT Kerberos implementation as its Kerberos infrastructure of
choice. The Samba build in Fedora is using MIT Kerberos implementation in order
to allow system-wide interoperability between both desktop and server
applications running on the same machine.

At the moment the Samba Active Directory Domain Controller implementation is
not available with MIT Kereberos. FreeIPA and Samba Team members are currently
working on Samba MIT Kerberos support as this is a requirement for a GNU/Linux
distribution integration of Samba AD DC features.

We have just finished migrating the file server and all client utilities to MIT
Kerberos.  The result of this work is available in samba-* packages in Fedora.
We'll provide Samba AD DC functionality as soon as its support of MIT Kerberos
KDC will be ready.

In case of further questions do not hesitate to send your inquiries to
samba-owner@fedoraproject.org

```

