# PKS Flannel CIDR override

## What does this do?

PKS has a bug (June 2020) where the master node Pod Network CIDR is always 10.200/16.  If you override this in the PKS tile and need to use features like Admission Controller webhooks, you'll see the master node call the Pod from a 10.200.x.x source IP address.  This might violate security policies, etc.

This addon will force the PKS install's Pod CIDR to be set correctly on the master node.

## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your PKS bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc-pivotal/pks-flannel-master-cidr-release && cd pks-flannel-master-cidr-release
bosh create-release --force
bosh upload-release ./dev_releases/pks-flannel-cidr/pks-flannel-cidr-0+dev.1.yml 

```
4. Configure the addon from this repo
```
bosh -n update-config --name=pks-flannel-cidr --type=runtime ./addon.yml
```
5. Update your PKS clusters via the PKS CLI and/or Ops Manager "Apply Pending Changes" button with the PKS upgrade errand enabled.  This addon will automatically be installed on all worker nodes with the default manifest `pks-dpw-manifest.yml`



