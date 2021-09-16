# MooseFS TM drivers for OpenNebula

WORK IN PROGRESS, still testing

Moved back from LizardFS to MooseFS by Michal Leinweber (ASW Systems a.s.) 2021

Based on the original OpenNebula code, changes by Carlo Daffara (NodeWeaver Srl) 2017-2018

For information and requests: info@nodeweaver.eu

To contribute bug patches or new features, you can use the github Pull Request model.

Code and documentation are under the Apache License 2.0 like OpenNebula.

Compatible with MooseFS 3.x and OpenNebula 6.x
Prerequisites:
* mfs commands executable by the user that launch the OpenNebula probes (usually oneadmin) and in the default path 
* the moosefs datastore must be mounted and reachable from all the nodes where the TM drivers are in use, and with the same path

The TM drivers are derived from the latest 5.4.3 "shared" TM drivers, with all the copies and other operations modified to use the 
live snapshot feature of MooseFS.

## To install in OpenNebula:
Copy the drivers in /var/lib/one/remotes/tm/moosefs

```
# fix ownership
# is you have changed the default user and group of OpenNebula, substitute oneadmin.onedadmin with <installationuser>.<installationgroup>
chown -R oneadmin.oneadmin /var/lib/one/remotes/tm/moosefs
```

in the section
```
TM_MAD = [
    executable = "one_tm",
    arguments = "-t 15 -d dummy,lvm,shared,fs_lvm,qcow2,ssh,vmfs,ceph,dev,moosefs"
]
```
(or the name you use for the moosefs repo)

if you want to add Datastore support:
Create a moosefs directory in the datastore directory, and add the files in the repo
https://github.com/ASWsystems/moosefs-DM_MAD
In the one.conf file add "moosefs" in the section
```
DATASTORE_MAD = [
    executable = "one_datastore",
    arguments  = "-t 15 -d dummy,fs,vmfs,lvm,ceph,dev,moosefs"
]
```

and

```
TM_MAD_CONF = [
    NAME = "moosefs",
    LN_TARGET = "NONE",
    CLONE_TARGET = "SYSTEM",
    SHARED = "YES",
    DS_MIGRATE = "YES",
    ALLOW_ORPHANS = "YES"

    TM_MAD_SYSTEM = "shared",
    LN_TARGET_SHARED = "NONE",
    CLONE_TARGET_SHARED = "SYSTEM",
    DISK_TYPE_SHARED = "FILE"    
]

DS_MAD_CONF = [
    NAME = "moosefs",
    REQUIRED_ATTRS = "",
    PERSISTENT_ONLY = "NO",
    MARKETPLACE_ACTIONS = "export"
]

```
(the TM_MAD_CONF and DS_MAD_CONF were graciously highlighted by GitHub user https://github.com/ioiioi )

restart opennebula:
```
service opennebula restart
service opennebula-sunstone restart
```
As oneadmin user sync the remote scripts:
```
su - oneadmin -c 'onehost sync --force'
```

Thanks to 


