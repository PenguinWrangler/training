# Dell Unity Training
## Course Intro and System Administration

UEMCLI
- Download UEMCLI client from Dell website
- UEMCLI can be used to script tasks (What would we use this for?)
- ``` uemcli [<switches>] <object path> [<object qualifier>] <action> [<action qualifiers>] ```
    - Switches: Used to access a system, upload files to the system, and manage security certificates.
    - Object: Type of object on which to perform an action, such as a user, host, LDAP setting.
    - Object qualifier: Unique identifiers for objects in the system. The format is <identifier> <value>.
    - Action: Operations that are performed on an object or object type. Examples of actions are create and set.
    - Action qualifier: Parameters specific to actions. Examples of action qualifiers are -passwd and -role.
        - example: ``` uemcli -d 192.168.1.230 -u local/admin -p Password /sys/general show -detail ```
        - unsure what this does - what is /sys/general?
        - why do I need the UEMCLI client?
        - really need some man pages here.
</br>
</br>

REST API
- https://www.dell.com/support/manuals/en-us/unity-400/unity_p_restapi_prog_guide/rest-api-overview?guid=guid-8b0e9223-4170-417f-aa8a-be9ae19e82ee&lang=en-us

</br>
</br>

User Accounts
- can create local user accounts - or LDAP authentication
    - local accounts are associated with roles

</br>
</br>

Unisphere Central
- What is unisphere central?
    - can we deploy this?
    - should we deploy it?
    - what does it give us that we currently don't have?

</br>
</br>

cloud IQ
- could possibly be useful if we move unisphere to internet facing connection.

</br>
</br>

NTP
- is our NTP configured? configured to which server?
- unisphere > settings > management > system time and NTP

</br>
</br>

FallBack Policy
- REMINDER - Check fallback policy
    - In Dell EMC Unity XT systems with dual SPs, when one of them has a problem or is rebooting, the NAS servers that are hosted on the SP fail over to the other SP.
        - Failback to the recovered SP can be automatic or manual depending on the failback policy set on the storage system.
        - To view or modify the failback policy of the Unity system:

        - Select the Failback Policy option under the Management section.
      Disable, or enable the automatic failback policy (the default is enabled).
            - When the option is disabled you can manually fail back all NAS servers, by selecting Failback Now.
        - Select Apply to submit and save the changes. The Failback Policy is set for the storage system.

</br>
</br>

ALERTS
- active manual vs active auto
    - active_manual means it is active and even if the condition is resolved the alert must manually be cleared. 
    - active_auto means the alert will resolve itself after the condition is met.
- alerts can be set up to email - if there is an smtp server available.
- alerts can also be sent to snmp targets
    - settings > alerts > snmp
    - can do v2c or v3.0 and be sent based on alert level
  - **REMINDER - explore configuring snmp alerts to send to zabbix**

logging
- settings > management > remote logging
- logs can be sent to syslog server
- up to 5 remote log servers can be specified

System Administration Key Points

- User Interfaces and Access Control
    - Configuration and management of the Dell Unity family of storage systems is performed using three interfaces: Unisphere, UEMCLI and REST API.
    - Access to Dell Unity XT systems is granted to defined and configured user accounts (local or LDAP). The user accounts are role based.
    - Dell Unity XT systems can be monitored through the Unisphere Central and CloudIQ applications.
- Basic System Settings
    - Dell EMC XT system global settings and parameters such as System time and DNS can be configured from Unisphere Settings.
    - The Unisphere Settings window also provides options to configure the time zone for snapshot schedules and asynchronous replication throttling.
    - The Dell Unity XT management port network address, and the failback policy can be configured from the Unisphere Settings.
- Support Configuration
    - A Proxy server can be configured to exchange service information for Dell Unity XT systems that cannot connect to the internet directly.
    - Support credentials are used to retrieve the customer current support contract information and keep it updated automatically.
    - Contact information ensures that Dell support has the most accurate information for contacting the user in response to an issue.
    - Storage administrators can view the status and enable the Secure Connect Gateway (Secure Remote Services) feature from the Support Configuration section of Unisphere settings.
        - There are two Secure Connect Gateway deployment options available for the Dell Unity family of storage systems: centralized and integrated.
        - There are two remote service connectivity options for Integrated Secure Connect Gateway: Outbound/Inboud or Outbound only.
- Unisphere Alerts and Event Monitoring
    - Unisphere alerts are usually events that require attention from the system administrator.
    - The alerts severity levels are categorized as Information, Notice, Warning, Error, and Critical.
    - There are four states for alerts: Active_Manual, Active_Auto, Inactive, and Updating.
    - Alert details provide time of the event, severity level, alert message, description of the event, acknowledge flag, component affected by the event, and status of the component.
    - Unisphere can be configured to send the system administrator alert notifications via email or though an SNMP trap.
    - Users can monitor when jobs are active, complete, or failed. The jobs page shows the number of active jobs: queued or running. The system polls for active jobs every 10 seconds and updates the active jobs count.
    - Unisphere can be configured to send log message entries of a determined severity level to a remote server
 
 </br>
 </br>

 ## Storage Provisioning

 Supported Storage Resources
- storage pools
    - dynamic
    - traditional
- block storage
    - Luns
    - consistency groups
    - Thin clones
- file storage
    - NAS Servers
    - File Systems
    - Shares
- Vmware datastores
    - VMFS
    - NFS
    - vvol datastores

- Luns and consistency groups provide block level storage  to hosts and applications
    - via fibre channel or iscsi 
- file systems provide network access
    - smb via windows
    - nfs for linux / unix
- vmware datastores
    - accessed via FC or iscsi via vmfs
- vvols 
    - can be block or file datastores. unsure of how this relates to what we do. need to explore further
    - **REMINDER - look into vvol storage**

</br>
</br>

Storage Pools
- all storage resources are provisioned from storage pools
    - unified storage pools
    - pools are created from groups of disks
    - no need to create different pools for different resource types (block or file resources)
 
 </br>

- storage pools can be heterogenous or homogenous
    - Homogenous pools only have one type of disk
        - Extreme Performance - all flash
        - performance - SAS drives
        - capacity - NL SAS drvies
    - heterogenous pools can be set up as tiered storage containing different types of drives.
        - with the correct licensing each tier can be associated with a raid type

</br>

**Warning: SAS Flash 4 drives cannot be part of a heterogeneous pool. These drives can only be part of a homogeneous All-Flash pool.**

</br>

- All-Flash Arrays (AFA) use dynamic pools by default
    - Unisphere CLI and REST API are the same for dynamic and traditional pools

 </br>

- Dynamic Pool Benefits
    - Dynamic pools are not limited by traditional RAID technology, and provide improved storage pool planning, provisioning, delivering a better cost per GB.

        - Users can provision pools to a specific capacity without having to add drives in specific multiples. For example, the expansion by 4+1 or 8+1.
        - Users can expand pools by a specific capacity, generally by a single drive, unless crossing a drive partnership group.
        - Different drive sizes can be mixed in dynamic pools.

 </br>

 - Another major benefit is the time that it takes to rebuild a failed drive.

    - Dynamic pools reduce rebuild times by having more drives engaged in the rebuild process.
        Data is spread out to engage more drives.
    - Multiple regions of a drive can be rebuilt in parallel.
        The process increases rebuild performance with increased drive counts
        Reduces exposure of a second drive failure.

 </br>

- Dynamic Pools support raid 5, 6, 1/0
- Raid Width is selected automatically when the pool is created
    - based on number of drives and raid level
    - example: if 11 drives are chosen with raid 5, a width of 8+1 is selected.
    - **REMINDER - not understanding how they calculate this. More reasearch is needed**
- dynamic pools also support drives with different capacities (but the same interface type) within a group.
- the larger drives must be the majority or the drive capacity of the larger disks will not be reflected in the usable capacity section.
- The storage administrator must select the RAID type when creating a dynamic pool.

 </br>

- The system automatically populates the RAID width which is based on the number of drives in the system.

    - The example shows a RAID 5 (4+1) configuration in a drive partnership group.
    - At the physical disk level, the system splits the whole disk region into identical portions of the drive called drive extents
        - A drive extent is a portion of a drive in a dynamic pool. Drive extents are either used as a single position of a RAID extent or can be used as spare space. The size of a drive extent is consistent across drive technologies or drive types.
       
       </br>

        - Drive extents hold a position of a RAID extent or are held in reserve as a spare space
            - Spare space refers to drive extents in a drive extent pool not associated with a RAID Group. Spare space is used to rebuild a failed drive in the drive extent pool

         </br>

        - The drive extents are grouped into a drive extent pool
            - Management entity for drive extents. Tracks drive extent usage by RAID extents and determines which drive extents are available as spares.
    
     </br>

    - The drive extent pool is used to create a series of RAID extents
        - A collection of drive extents. The selected RAID type and the set RAID width determine the number of drive extents within a RAID extent.
        - Each RAID extent contains single drive extent from a specific number of drives equal to the RAID width.
        - RAID extents can only be part of a single RAID Group and can never span across drive partnership groups.
        - RAID extents are then grouped into one or more RAID Groups
            - A Dynamic pool RAID Group is a collection of RAID extents, and it can span more than 16 drives. The RAID group is based on dynamic RAID with a single associated RAID type and RAID width.
            - The number of RAID Groups and the size of the RAID Group can vary within a pool. It depends on the number of drives and how the pool was created and expanded.

     </br>

    - The process creates a single private LUN for each created RAID Group by concatenating pieces of all the RAID extents and striping them across the LUN.
        - A single dynamic pool private LUN is created for each dynamic RAID Group.
        - The size of a private LUN is the number of RAID extents that are associated with the private LUN. A private LUN may be as small as the size of a single drive.

        - The LUN is partitioned into 256 MB slices. The system distributes the slices across many drives in the pool.
        - The 256 MB slices are the granularity that the slice manager operates and storage resources are allocated.
- All flash storage arrays will only show extreme performance types.

 </br>

- A dynamic pool can expanded up to the system limits by one or more drives under most circumstances.
    - Expansion is not allowed if the number of drives being added is more than enough to fill a drive partnership group, but at the same time not enough to also fulfill the minimum drive requirements to start another drive partnership group.
        - The maximum size of a drive partnership group is 64 drives.
        - The minimum number of drives to start a new partnership group is the RAID width+1 requirement for spare space.

- when expanding a dynamic pool the number of drives you add determines how long until the space is available for use because the drive extents are "re-balanced" among the drives.

- When extra drives increase the spare space requirement, a portion of the space being added is reserved equal to the size of one drive.  This space reservation can occur when the spare space requirement for drive type -1 for 31 drive policy is crossed.
    
     </br>
    
    - **^^^^^I have no idea what this means. I need to look this up.^^^^^**

    </br>

- need to go back over the section of rebalancing drive extents. I have no idea what I am looking at.

- when adding drives of a different size you must add drives equal to the raid width in order to benefit from all of the space.

- Luns are block storage presented to hosts - must have 1 storage pool configured to create a LUN

- LUNs can be thin or thick provisioned.
    - the choice to thick provision can only be made at the time of creation. You cannot change it later.
    - LUNs are thin provisioned by default

 </br>

- During LUN creation you can specify a replication point
    - could this be useful to us at some point if we need a warm site?

 </br>

 - unsure of what use consistency groups would be to us.
    - from some further reading a consistency group is a way to make sure that disks are in sync when replicating
    - possibly have something to do with the latency / difference between what is in cache and what is written to the storage

 </br>

block storage access
- iscsi initiators / IQNs are generated and presented by the hosts and registered to the unity system

- requirments  for hosts to access storage
    - must have luns or consistency groups configured
    - in a fiber channel environmen an HBA must be present or in the case of ISCSI a standard nic with a software adapter
    - multipathing should be enabled
    - in a FC environment zoning must be configured on the switches
    - in iSCSI, initiators and targets must be established

- for idea performance with block storage presentation
    - for iscsi the host should be on a local subnet with each iscsi interface it is providing storage for
    - iscsi interface and hosts should have their own private network
    - in multipath environment, the SP should have two IP addresses (one for each path) in different subnets

- after configuration you must register the hosts with unity.
    - windows and linux is a manual process.
    - esxi registration is automatic

host to storage connectivity
- guidelines
    - single host should only connect with one protocol (FC, iSCSI) to a single array
    - a host may connect to different arrays with different protocols.
    - iscsi should use all HBA or all NIC connections - no mixing in host (**REMINDER : what does this mean?**)

- Initiators (FC or iscsi) are endpoints from which sessions originate
    - each initiator has a unique WWN (world wide name - for fibre channel)
    - or a unique IQN (iscsi qualified name)

- DEFINE - CNA - Converged Network Adapter. Can do ISCSI or Fibre Channel

</br>

Host iSCSI Initiator Registration Process – Linux Host
- The iSCSI driver open-iscsi comes with the Linux kernel.
- The Linux operating system includes the iSCSI initiator software.
    - Configure the open-iscsi driver with the network parameters for each initiator that connects to the iSCSI storage system.
    - Dell Technologies recommends changing some driver parameters. The configuration file is /etc/iscsi/iscsi.conf *
    - Each host connected to an iSCSI storage system must have a unique iSCSI initiator name for its initiators.
    - Multiple hosts connected to the iSCSI interface have the same iSCSI initiator name.

- Connects to an external iSCSI-based storage array through an Ethernet network. Example:

    - Host: ``` iqn.1994-05.com.linux1:2246335fe96e ```
    - Target Port: ``` iqn.1992-04.com.emc:cx.virt1619dzgnh7.a0 ```

- To view the iSCSI initiator name
    - Use the cat command to check the ``` /etc/iscsi/initiatorname.iscsi ```

- To view and discover the target array

     - Command: ``` iscsiadm -m discovery -t sendtargets -p <target ip port addr> ```
     - In the example, the iscsiadm command was used to discover target port 192.168.32.91. The discovery shows all connected ports on the array (apm00172445908), each entry represents an initiator path to the storage system. Each target port displays the array port and SP at the end of the entry. For example, a0, a1, b0, and b1.

</br>

- Note: The Linux iSCSI driver gives the same name to all NICs in a host.
    - This name identifies the host, not the individual NICs.
    - When multiple NICs from the same host are connected to an iSCSI interface on the same subnet, only one NIC is used. 
    - The other NICs are in standby mode.
    - The host uses one of the other NICs only if the first NIC fails.

</br>

Host Groups
- Hosts can be grouped for access to specific luns
- ex - if you have a clustsr that needs access to LUN 1, but not LUN 2, you can configure that accordingly with host groups instead of by host.

</br>
</br>

File Storage Provisioning
- Can be configured as
    - NAS Server
    - File System
        - file based storage resource with a specified storage quantity over a particular protocol
        - **REMINDER - truthfully unsure what this means. need to research more.**
    - Shares
        - SMB or NFS


</br>
</br>

Provision VMware Datastores
- unity discovers esxi hosts that are managed by a vcenter server
- pools are used to build LUNs > LUNs are used to build Datastores
- Datastores can be defined as
    - vmfs (block data)
    - NFS (file)
    - vvol (block)
    - vvol (file)

- a storage pool must be associated with a "capability profile" to enable vvols based storage
    - **REMINDER - look into capability profiles**
    - **REMINDER - read more about protocol endpoints re: vvol datastores**
    - **REMINDER - research benefits of vvol (file or block) vs nfs vs iscsi**
    - **REMINDER - what do storage profiles do?**

- datastores have owners - one of your storage processors

</br>
</br>

Storage Provisioning Key Points
- Storage Resources
    - Dell Unity XT storage resources are categorized in storage pools, block storage, file storage, and VMware datastores.
    - Dell Unity XT systems share pools with all the resource types: file systems, LUNs, and the VMware datastores.
    - Storage pools are created using the SAS Flash, SAS, and NL-SAS drives.
- Dynamic Pools
    - Dynamic Pools are supported on Dell Unity XT physical hardware only
    - All pools that are created with Unisphere on a Dell Unity XT AFA and HFA systems are dynamic pools by default.
    - Dynamic pools reduce rebuild times by having more drives engaged in the rebuild process.
        - Data is spread out through the drive extent pool.
        - The system uses spare space within the pool to rebuild failed drives.
    - A dynamic pool can be expanded by one or more drives to the system limits.
- Traditional Pools
    - The Dell UnityVSA uses traditional storage pools by default.
    - Traditional pools can be created on Dell Unity XT systems using the UEMCLI, or REST API interfaces.
    - Traditional pools can be homogeneous (built from single tier) or heterogeneous (multi-tiered).
- Block Storage Provisioning
    - Block storage resources that are supported by the Dell Unity XT platform include LUNs, Consistency Groups, and Thin Clones.
    - LUNs created from traditional heterogeneous pools, use FAST VP tiering policies for data relocation: Start High and Then Auto-Tier, Auto-tier, Highest Available Tier, and Lowest Available Tier.
    - Dell Unity XT front-end interfaces must be prepared for supported host connectivity protocols.
    - Hosts must have an adapter to communicate over the storage protocol: HBA (Fibre Channel ) or NIC for (iSCSI).
    - Connected hosts must have an initiator (iSCSI IQN or FC WWN) that is registered on the Dell EMC Unity XT storage system.
        - Host configurations are profiles of the hosts that access storage resources using the Fibre Channel or iSCSI protocols. 
        - Before a host can access block storage, you must define a configuration for the host and associate it with the provisioned storage resource.
        - A host group is a logical container which groups multiple hosts and block storage resources.
- File Storage Provisioning
    - File storage in the Dell Unity XT platform is a set of storage resources that provide file-level storage over an IP network.
    - A NAS Server provides file data transfer and connection ports for users, clients and applications that access file systems.
    - SMB and NFS shares are created for the file systems and provided to Windows, Linux, and UNIX clients.
        - NFS shares use host configurations  to grant access to Linux and UNIX NAS clients.
        - NFS clients have a host configuration profile on the storage system with the network address and operating systems defined.
        - SMB clients do not need a host configuration to access the file system share. 
- VMware Datastores Provisioning
    - Dell Unity family of storage systems supports the provisioning of specialized VMware storage resources called datastores.
    - The supported datastore types that can be provisioned In Unisphere or through CLI interfaces are VMFS (BLock), NFS (File), vVol (Block), and vVol (File).
    - Capability profiles must be associated with storage pools used for vVol datastores, in order to enable the storage policy-based provisioning of Virtual Machines.
    - The ESXi host must have an adapter to communicate over the storage protocol: HBA (Fibre Channel) or standard NIC (iSCSI of NFS).
    - Host configurations for ESXi hosts are configured by adding the vCenter Server and selecting the discovered hypervisor. The host configuration can then be associated with a VMware datastore.
    - Protocol Endpoints establish a data path between the ESXi hosts and the respective vVol datastores.


</br>
</br>

## Scalability, Performance, and Compliance

Fast Cache
- **performance feature for hybrid unity xt systems**
- consists of one or more pairs of raid1 SAS flash drives
- identifies when a chunk of data on lun is accesssed frequently
- system copies that data to fast cache
- can be larger than dram capacity
- fast cache buffers data waiting to be written to disk
- this results in less load on disks where the lun is contained
- copied in chunks of 64KB

</br>

Fast Cache Componenets
- Policy Engine
    - monitors and manages IO flow through fast cache
    - determines when data is a candidate for promotion by keeping statistical info on data
    - chunks are marked for promotion when a block is accessed 3 times in a short time period
    - chunks are then copied to fast cache and the memory map is update
    - policy engine settings are defined by system and cannot be changed
- Memory Map
    - contains info on all 64KB chunks located in fast cache
    - memory map is updated each time a block is added or replaced in fast cache
    - Memory Map resides in DRAM *and* on the system drive for HA reasons
    - SP memory is dynamically allocated for fast cache memory map
    - when IO reaches fast cache memory map is checked, then the IO is either redirected to a fast cache location, or to the storage pool.

</br>

Fast Cache Operations
- Host Read/Write
- Fast Cache Promotion
- Fast Cache Flush
- Fast Cache Cleaning
    - proactively copies dirty pages to physical devices during down times

</br>

Host IO Limits
- Also referred to as QOS
- limiting IO allows more predictable performance
- to activate
    - enable global feature
    - create policies
    - assign to storage resource
- Can be set as throughput (IOPS) or bandwidth KB or MB per second
- can also be set as a combination of both
    - if a combination is set, the IO is limited based on which policy is reached first
- only one IO limit can be applied to a storage resource
    - for example - if an IO limit is applied to a group of LUNs, then you cannot apply another one to any of the individual LUNs in that group.

</br>

Use Cases
- controlling maximum level of service
    - if your SLA with a customer is for 500 IOPS you can limit to that.
- Billing rates
- runaway processes
- busy users (taking resources from other things)
- Test and Dev

</br>

Policy Types
- Absolute
    - IO Traffic Threshold
- Density-based
    - scales with the amount of storage allocated to the resource
    - IOPS per GB

</br>

- shared policies cannot be changed after creation.
    - new policies must be made to replace them instead
- Multiple resources can be added to a single policy - even different sizes

</br>

Burst Feature
- allows for one time exception to policies
    - allows to catch up backlog periodically
    - ability for service provicers to upsell
- Burst settings are configured when creating a host IO limit policy
- can also be added to existing policies
- Burst is set like this
    - Burst percent to increase (1% to 100%)
    - for what duration (1 min to 60 mins)
    - every (hours) (1 hour to 24 hours)
        - example - allow burst mode to consume up to (50%) more than allocated for (10 minutes), every (two hours).

</br>

Policy Level Controls
- Host IO limits can be paused or resumed
- Individually - even for shared policies
- Statuses: Active, Paused, Global Paused
- Can be set in Management > performance

</br>

UFS64 File System Extension and Shrink
- Unity systems use the UFS64 architecture
- allows users to extend file systems
- extension operations are transparent to the client
- on physical systems maximum file system size 256TB
- can be performed on thin or thick provisioned file systems
- thin provisioned file systems can be set to auto expand based on ratio of provisioned to used space - 75% capacity by default
- auto extension does not change advertised capacity
- manual extension of thin provisioned file systems increases the advertised size and available size, without increasing size on disk.
- auto extension cannot exceed FS visible size. that requires manual extension.

</br>

File Level Retention
- locks files to protect from deletion / modification
- specified retention date and time
- enabled at file system creation
- cannot be disabled
- can either be
    - FLR-E (enterprise)
    - FLR-C (compliance)
- FLR file states
    - Not Locked
    - Append Only
    - Locked (WORM)
    - Expired
        - cannot be modified or renamed, but can be deleted
        - retention period can be extended and file transitions back to locked state
- For SMB, NFS, FTP clients
- FLR-C supports read-only snapshots, but not snapshot restores
- FLR-E supports read-only and RW snapshots, as well as snapshot restores
- supports dell data reduction features.
- not supported on NFS datastores
- when FLR-C is enabled admin must also enable data integrity check
    - to check if writeverify is enabled
        - ``` svc_nas *nas_name* -param -f FLRCompliance -i writeverify ```
        - output should give a "default_value" and "current_value"
        - 0 for disalbed, 1 for enabled
    - to enable writeverify
        - ``` svc_nas *nas_name* -param -f FLRCompliance -m writeverify -v 1 ```
        - unsure what -m and -i are, but assuming -m is modify and -v is setting the value to 1
- dell FLR toolkit for SMB
    - software for windows where admins can make flr files and set attributes
- to lock a file in unix use 
    - ``` touch -at 202412312359 lockedfile ```
        - this sets the last accessed time to Dec 31 2024 at 23:59
        - this date should match your retention date
    - ``` chmod -w lockedfile ```
        - this takes away write permissions from the file
- to set file to append only
    - ``` touch *filename* ```
    - ``` chmod -w *filename* ```
    - ``` chmod +w *filename* ```
        - **REMINDER - I don't understand what's going on here. Isn't this just taking away and giving back write permissions?** 

</br>
</br>

Scalability, Performance and Compliance Key Points
- FAST Cache
    - FAST Cache is a secondary cache created from SAS Flash 2 drives that extends the storage system caching capacity.
    - The storage system identifies LUN data that that is more frequently accessed and service any subsequent requests from the FAST Cache.
    - FAST Cache operations includes host reads/writes, FAST Cache promotion, FAST Cache flush, and FAST Cache cleaning. In addition, the FAST Cache can be expanded, shrunk or deleted.
- Host I/O Limits
    - Dell Unity XT Host I/O Limits is a feature that limits I/O to storage resources: LUNs, attached snapshots, and VMFS datastores.
    - Only one Host I/O limit policy can be applied to a storage resource. Policies can be set by:
        - Throughput, in IOs per second (IOPS)
        - Bandwidth, defined by Kilobytes or Megabytes per second (KBPS or MBPS)
        - A combination of both types of limits
    - There are two Host I/O Limit policy types: Absolute and Density-based. In addition, a policy can share the same limit(s) with all assigned storage resources.
    - The Burst feature allows for one-time exceptions to Host I/O Limits. The feature can be  set at some user-defined frequency for each Host I/O Limit policy. 
- UFS64 File System Extension and Shrink
    - In Dell Unity XT systems, the UFS64 architecture enables the extension of file systems.
        - A storage administrator can manually extend the size of a provisioned file system.
        - Thin file systems are automatically extended by the system based on the ratio of used-to-allocated space.
    - In Dell Unity XT, the UFS64 architecture enables the reduction of the space the file system uses from a storage pool.
        - A storage administrator can manually shrink the size of a provisioned file system.
        - Thin-provisioned file systems are automatically shrunk by the system when the used space is less than 70%. 
- File-level Retention (FLR)
    - Dell Unity XT supports the configuration of File-level Retention (FLR) during the creation of a file system.
    - File-level Retention (FLR) protects files from modification or deletion through SMB, NFS, or FTP access based on a specified retention date and time.
    - There are two different types of FLR; FLR-E (Enterprise) and FLR-C (Compliance).
    - Files within an FLR enabled file system have different states; Not Locked, Append-only, Locked and Expired.

</br>
</br>

## Storage Efficiency

Data Reduction
- reduces the amount of physical storage required for a set of data
- Data Reduction Methods
    - Deduplication
        - stores only a single instance of a data pattern
    - Zero Detection
        - logically detects consecutive zeroes
        - saves only one instance and user pointers
    - Compression
        - encodes data using fewer bits than originally
    - Advanced Deduplication
        - dedups data blocks within a given storage resource that do not contain internally-defined data patterns
- licensed with all physical unity systems, no extra cost
- supported on all flash and on hybrid pools
- provies data reduction savings on snapshots and thin clones
- not available on dell unity vsa
- to support data reduction a pool must contain a flash tier (on hybrid systems)

</br>

Data Reduction Theory of Operation
- Occurs during the System Cache proactive cleaning operations
- or during system cache flush to disk
- can either be updated data or new data
- live data in caches is uncompressed
- compression occurs before data is written to storage pool
- multiple blocks are aggregated and sent through the algorithm
- after determining if storage savings can be made or data must be written, space is allocated, then written to

</br>

Deduplication
- data sent to reduction algorithm during cleaning or flushing of write cache
- if a duplicate pattern is detected private space metadata is updated to include pattern referencea nd how to rebuild if data is called again
- if pattern is found, remainder of data reduction features are skipped
- if pattern is not found data is passed through to the compression algorithm

</br>

- you can check the data reduction savings (in GB) on the lun page.
- deduplication / replication
    - all deduplicated data is re-hydrated / uncompressed before replication begins

</br>

Fast VP
- Fully Automated Storage Tiering for Virtual Pools
- not applicable to all-flash systems
- Typically data is used accessed shortly after it was created and activity level decreases as data ages
- FAST VP helps maintain performance with slower disks
Tiering Policies
- Highest Available
    - quick response times are a priority
- Auto-Tier
    - data is initially placed where storage has most capacity, then moved based on activity
- start high then auto
    - starts on fastest storage then moved based on activity
- lowest available
    - slowest storage with the requisite space

</br>

Thin Clones
- thin block storage resource that shares blocks with its parent resource
- base lun family includes the base lun and all of its derivitive snapshots and thin clones
- data available from the snapshot is immediatly available to the thin clone
- thin clones are created from attached read-only or unnattached snapshots with no auto-deletion policy
- **REMINDER - what are the use cases here? could we use this for testing applicaitons?**
- limitations
    - 16 thin clones per base lun
    - 256 snapshots per lun
    - 256 snapshots + thin clones per lun
- used to make space efficient copies of the production environment
    - **REMINDER - how does this impact performance?**
- Recommended uses
    - Dev and Test environments
        - work with real workloads and all data services with no effect on production
    - Parallel Processing
        - if multiple servers process datasets thin clones can achieve this more quickly
        - **REMINDER - would love an exmple here.**
    - Online Backup
        - Maintain Hot backup copies of production systems
        - If there is production data corruption immediately resume the read/write workload by using thin clones
        - **REMINDER - how is this achieved?**
    - System Deployments
        - build and deploy templates for near identical environments
- snapshots can be refreshed only from the base lun
- thin clones can be refreshed from any snapshot

</br>

Thin Clone Considerations
- certain properties cannot be changed at the thin clone level
    - dependent on the base lun
    - if changes are made to the base lun, they reflect in the thin clone
    - including
        - SP Ownership
        - FAST VP
        - Data Reduction settings
- thin clones are not supported for snapshots of thick provisioned luns
- you cannot create a thin clone of a thin clone - must include an intermediary snapshot
- the source snapshot must be read-only and automatic deletion must be disabled
- a replicated thin clone becomes a full clone on the destination storage system
    - **REMINDER - this is interesting, because this changes total storage volume on replication.**
- Unisphere move(?) option is not supported for thin clones
- **REMINDER - need to do more research on how refresh operations work. Dell's guidance was not clear to me**
- Data Reduction and Advanced Dedup are suppored with both snapshots and thin clones

</br>

File System Quotas
- enable tracking of file system usage
- can be used to limit users or a directory tree
- can be specified as file size quota, or blocks policy.
    - file size is calculated in 1KB incrments
    - block quota policy is done in 8KB units
- quotas can have hard and soft limits
- select file system to edit > quotas tab > manage quota settings
- soft limit
    - when threshold is exceeded a countdown timer to resolve the storage issue is started
    - this countdown timer is called the "grace period"
    - grace period can be limited by hours, days, minutes or unlimited.
    - when set to unlimited data can be written until the hard quota is met
- hard limit
    - no new data can be written after hard quota is met
- **REMINDER - what is the use case here?**
- **Could this be used in conjunction with vvols(?) (still unsure how those work) to limit virtual machine size? I'm not sure what purpose that would serve. need some practical examples.**


</br>

Storage Efficiency Key Points

- Data Reduction
    - Dell Unity XT Data Reduction provides space savings by using data deduplication and compression.
    - Data reduction is supported on All Flash pools created on Dell Unity XT Hybrid Flash systems or Dell Unity XT All Flash systems.
    - Data reduction is supported on thin storage resources: LUNs, LUNs within a Consistency Group, file systems, and VMware VMFS and NFS datastores.
- FAST VP
    - Dell Unity Fully Automated Storage Tiering for Virtual Pools (FAST VP) monitors the data access patterns within heterogeneous pools on the system.
    - In storage pools with Flash, SAS and NL-SAS, FAST VP uses the Flash drives for active data, and low cost spinning drives for less active data. 
    - There are four tiering policies: Start High then Auto-tier (default), Highest Available tier, Auto Tier, Lowest Available Tier.
    - RAID levels for each tier can be selected when creating a pool. The supported RAID levels are RAID 1/0, RAID 5 and RAID 6.
    - Data relocation can be schedule the system level, or manually started at the storage pool level.
- Thin Clones
    - A Thin Clone is a read/write copy of a thin block storage resource that shares blocks with the parent resource. 
        - The resource is built from a snapshot of thin block storage resources: LUNs, LUNs member of a Consistency Group, or VMFS datastores.
        - Thin Clones can be created from Attached read-only or Unattached Snapshots with no auto-deletion policy and no expiration policy.
        - Thin Clones are supported on all Dell EMC Unity XT models including Dell EMC UnityVSA.
- File System Quotas
    - Dell Unity XT systems support file system quotas which enable storage administrators to track and limit usage of a file system.
    - Quota limits can be designated for users, a directory tree, or users within a quota tree.
    - Quota policy can be configure to determine usage per File Size (the default), or Blocks.
    - The policies use hard and soft limits set on the amount of disk space allowed for consumption.

</br>
</br>

## Data Mobility

Local LUN Move
- Moves LUNs between different pools within the same system
- Or move LUNs within the same pool of a system
    - **REMINDER - why would you want to do this?**
- Operation is transparent to the host and has minimal performance impact
- provides load balancing between pools
    - if one pool is reaching capacity and another has plenty of space, the feature can be used to move a LUN and more evenly distrubute the storage
- can also be used to change storage characteristics of a LUN
    - change disk types or raid types
    - change a thin lun to thick lun or the reverse
    - data reduction of existing thin lun without data reduction enabled
- when a lun is moved it retains its attributes and some of the extra configurations
    - things like
        - size
        - name
        - LUN type
        - LUN metrics
        - HLU (?)
        - Storage Processor Ownership
        - Unique ID
        - snapshot schedule (but not snapshots)
        - Host IO Limits
- uses dell TDX (transparent data transfer)
- must be standalone lun, or within a consistency group or vmfs datastores
- no thin clones or have derived thin clones
- cannot be in a replication session
- cannot be expanding or shrinking (active process)
- cannot be restoring from snapshot
- cannot be being imported from vnx (?)
- cannot be offline / requiring recovery
- you should not upgrade system during lun move session
- 16 active move sessions at a time
- 100 move sessions total
- define priority when creating session
    - idle
    - low
    - below normal
    - normal
    - above normal
    - high
- TDX is multithreaded
    - multiplexes 16 active sessions into 10 concurrent sessions based on priority

</br>

Local Nas Server Mobility
- can move nas server between storage processors
- changes ownership of nas to the peer SP
- transparent to nfs clients and smb3 clients (when running continuous availability)
- SMBv2 clients or v3 clients without CA running are disrupted due to protocol stateful nature
- can be used to balance load across SP
- can also be used to provide access during maintenance
    - **REMINDER - why don't they just tresspass?**
- single nas server at a time
    - simultaneous nas moves are not supported
- should only move nas that is in healthy OK state
- NAS server that is destination of file import session cannot be moved
- **REMINDER - what is a file import session**

</br>

Data Mobility Key Points

- Local LUN Move
    - The local LUN Move is a native feature of Dell Unity to move LUNs within a single physical or virtual Dell Unity system.
        - The feature moves LUNs between different pools within the system, or within the same pool.
        - The move operation is transparent to the host and has minimal performance impact on data access.
    - The moved LUN retains its LUN attributes and some extra LUN feature configurations, such as snapshot schedules.
    - The local LUN Move can be used to move LUNs to a pool that has more capacity, or to change the storage characteristics for a LUN.
- Local NAS Server Mobility
    - The local NAS Server mobility feature moves a NAS Server between the Dell Unity Storage Processors.
        - The operation effectively changes the ownership of the NAS Server to the peer Storage Processor.
        - The move is transparent to NFS clients and the SMB3 clients configured with Continuous Availability.
    - The NAS Server mobility feature can be used for balancing the load across the Dell Unity system Storage Processors.
    - The local NAS Server mobility feature supports moving only a single NAS Server at a time. The NAS server must be in a healthy OK state.


</br>
</br>

## Data Protection with Snapshots

Snapshots Overview
- snapshots can be eithr read/write or read-only
- snapshot usage
    - backups
        - assuming this means making backups from snapshots, not snapshots as backups.
    - Restores
    - Mining (?)
    - Testing
- snapshots are architected using redirect on write technology
- this avoids the performance penalty that copy on first write technology has when existing data is changed.
- Redirect on write
    - when a snapshot is taken the existing data on storage stays in place
    - snapshot provies a point in time view of the data
    - Production data access also uses this view to read existing data
    - no resource usage required
- copy on first write
    - a storage resource must be reserved to hold original data that changed to preserve the point in time view
    - **REMINDER - what does this mean?**
- **REMINDER - look up "redirect on write" architecture**
- in snapshot schedule you can set snapshots to start deleting the oldest at X% full, and delete until only Y% full
- vmfs / LUN snapshot operations
    - restore
    - attach to host
    - detach from host
    - copy
    - refresh and replicate
- nfs snapshot operations
    - restore
    - copy
    - refresh

LUNS and consistency group snapshots
- you can snapshot a consistency group to make a snapshot of a group of LUNS   
    - system completes any outstanding IO for the group of luns
    - writes to LUNs are suspended until the snap operation completes
    - creates a write-order consistent image of the group of luns
        - **REMINDER - what is a write order consistent image?**
- to resotre a lun snapshot hosts have to disconnect from snapshots
- then must stop all IO to the lun
- then disconnect host from lun
- restore snapshot
- reconnect host to lun
- can snapshot multiple point-in-time images

File System Snapshots
- very similar to LUN snapshots

vVol snapshots
- cannot create snapshots of config or swap vvols
- only creats snapshots of data (.vmdk) vvols

</br>

Data Protection with Snapshots Key Points

- Snapshots Overview
    - The Dell Unity XT Snapshots feature enables space efficient point-in-time copies of storage resources for block, file, and VMware "data" vVols.
        - The snap images can be read-only or read/write and used in various ways.
        - The production data can be restored from a snapshot to a known point-in-time data state.
    - Snapshots of storage resources (LUNs, file systems, and VMware datastores) are architected using Redirect on Write technology.
    - Snapshots can be scheduled at the time the storage resource is created, or manually created from the resource properties page.
    - Snapshot operations include Restore, Attach to host, Detach from host, and Copy, depending on the source storage resource.
- LUNs and Consistency Groups Snapshots
    - Dell EMC XT Snapshots provide a mechanism for capturing a snapshot of the multiple LUNs within a consistency group.
        - The operation captures a write-order consistent image of the group of LUNs.
        - Multiple hosts can be attached to any specific LUN snapshot or multiple snapshots within the tree.
    - Dell Unity XT system operations on LUN-based snapshots are Restore, Attach to host, Detach from host, and Copy.
        - The Restore operation rolls back the LUN or Consistency Group LUN members to the point-in-time data state captured in the snapshot.
        - The Snapshot Attach to host operation grants a host a defined access level to a LUN snapshot.
        - The Snapshot Dettach from host operation revokes the host access to the LUN snapshot.
        - The Copy operation makes a replica of an existing LUN snapshot which inherits the parent snapshot data state and retention policy.
- File System Snapshots
    - Dell Unity XT Snapshots provide a mechanism for capturing a snapshot of file systems.
        - Multiple snapshots of a file system capture different point-in-time data states.
        - Snapshots of file systems can be scheduled or manually created.
    - Snapshots of a file system can be created either as read-only or read/write, and are accessed in different manners.
        - Read/write snapshots of a file system can be shared by creating an NFS or SMB share.
        - The read-only file system snapshot is exposed to the client through a checkpoint virtual file system (CVFS) mechanism that Snapshots provides.
    - Dell Unity XT system operations on file system-based snapshots are Restore and Copy.
        - The Restore operation rolls back the file system to the point-in-time data state captured in the read-only or read/write snapshot.
        - The Copy operation makes a replica of an existing file system snapshot that is either read-only or read/write shared or unshared.
- Native vVol Snapshots
    - Dell Unity XT storage systems support creating snapshots of “data” (.vmdk) vVols in Unisphere, Unisphere CLI, or REST API.
        - Snapshots of "Config" and "Swap" vVols are not supported.
        - Snapshot schedules are not supported on vVols.
    - Dell EMC XT vVol snapshot restore operations are supported using Unisphere, Unisphere CLI, and REST API, or the vCenter Server.
        - vVol snapshots created in the vSphere environment can be restored using either vCenter Server or the Dell Unity XT management interfaces.
        - vVol snapshots created using the Dell EMC Unity XT management interfaces cannot be restored using vCenter Server.


</br>
</br>

## Data Protection and Replication

Replication Overview
- Not sure how helpful this will be as we do not have any replication sites
- syncronous replication
    - recommended 60 mile limit
    - sub 10ms latency recommended
- asyncronous
    - long distance
    - no latency impact

</br>
Asynchronous Local Replication
- Supported storage resources
    - LUNs
    - Consistency Groups
    - Thin Clones
    - VMware vStorage VMFS datastores
    - VMware NFS datastores
    - File systems
    - NAS servers
- Can be used to change disk type
- can be used to balance capacity

</br>

Remote Replication Overview
- Replicates storage between disparate systems
    - DR solution
    - access to data during planned downtime
- Supported storage resources for synchronous and asynchronous replication
    - LUNs
    - Consistency Groups
    - Thin Clones
    - VMware VMFS datastores
    - VMware NFS datastores
    - File systems
    - NAS servers

</br>

Synchronous Replication Overview
- uses write intent logs on each system to replicate
- there is a WIL for each SP on each system
- write intent logs hold fracture logs
    - fracture logs are designed to track changes to the source storage resource should the destination resource become unavailable
    - when it becomes reachable again sync recovers using the fracture log
- Synchronous replication can be one directional or bi-directional
    - one directional is used when one system is used for production workloads and the other is just a replication target (like a DR site)
    - bi directional replication is used when production is spread across multiple systems or locations updating data simultaneously
- it is not possible to replicate a single storage source to multiple destinations
- only for use with single source / destination pairs

</br>

Synchronous Replication Process
- create destination resource (that matches source)
- create Write intent logs
- initial copy
- host writes to source (and the process to maintain sync beings)
- writes replicated to destination (use WIL)
- destination acknowledges source
- source ack host
    - only after this whole process does the source send acknowledgement back to the host that the write operation has completed

</br>

Synchronous Replication States
- Session States
    - Active
        - normal operations
        - source and dest are in sync
    - Paused
        - replication has been stopped and sync state is "consistent"
        - indicates that the WIL is used to perform sync
    - Failed Over
        - A Failed Over session has one of two sync states.
        - can show an Inconsistent state meaning the sync state was not In Sync or Consistent before the Failover.
        - If the sync state was In Sync before the Failover, it will be Out of Sync after session Failover.
    - Lost Sync Communications
        - indicates destination is unreachable
- Sync States
    - In Sync
    - Syncing
    - Out of Sync
    - Consistent
    - Inconsistent

</br>

- **REMINDER - it apprears that replication can only be done over fibre channel**

</br>

Asynchronous Remote Replication
- One Directional
    - one to many
    - many to one
- uses snapshot states instead of write intent logs

- Asynchronous replication of snapshots
    - Primary storage resource must also be replicated
    - Unattached block and read-only file snapshots
    - Scheduled or user created snapshots
- Primary storage resource snapshots can be:
    - LUNs
    - Consistency Groups
    - Thin Clones
    - VMware VMFS datastores
    - VMware NFS datastores
    - File systems
- Replica retention policies can be customized
    - Cost savings
    - Compliance

</br>

Data Protection with Replication Key Points

- Replication Overview
    - The Dell Unity XT Replication feature creates synchronized redundant data replicas of storage resources within the same system or remote system.
    - Synchronous replication is a data protection solution for limited distances.
        - The operation ensures zero data loss between local source and remote replica.
        - Each block data written is saved to the local and remote system at the same time.
    - Asynchronous replication is primarily used to replicate data over long distances.
        - The write operations to the source are not instantly replicated to the remote replica.
        - All writes are tracked on the source and the deltas (data difference) are replicated to the destination during the synchronization cycles.
    - The Dell Unity XT platform supports the asynchronous local replication of supported storage resources between pools.
    - The Dell Unity XT platform supports asynchronous or synchronous remote replications of supported storage resources between storage systems.
    - Supported synchronous remote replication topologies are One-Directional, and Bi-Directional. - The supported asynchronous remote replication topologies are One-Directional, Bi-Directional,  One-to-Many, and Many-to-One.
    - The synchronous replication session states are: Active, Paused, Failed Over, or Lost Sync Communications. Depending on the session state, the sync states are In Sync, Syncing, Out of Sync, Consistent, or Inconsistent.
- Replication Configuration Process
    - Replication is configured when the supported storage resource is created or manually created from the resource properties page.
        - Supported block storage resources are LUNs or LUNs which are members of a Consistency Group.
        - Supported file storage resources are NAS server and associated file systems. (A session for the NAS server and a session for each associated file system are created.)
        - Supported VMware storage resources  are VMFS and NFS datastores.
    - Before you create a remote replication session, you must configure active communications channels between the two systems.
        - Asynchronous replications use an IP-based connectivity between the source and destination SPs.
        - Synchronous replications use FC-based connectivity between source and destination SPs.
- Asynchronous Replication Configuration
    - The configuration of an Asynchronous replication session between Dell Unity XT systems involve the following procedures.
        - Configuration of replication interfaces on source and destination systems.
        - Create a replication connection between the storage systems, and validate the connection from peer system.
        - Define the source and replication settings (mode, RPO, destination), and create the replication session.
- Synchronous Replication Configuration
    - The configuration of a Synchronous replication session between Dell Unity XT systems involve the following procedures.
        - Identify the Sync Replication Management FC ports on source and destination, and create replication interfaces.
        - Create a replication connection between the storage systems, and validate the connection from peer system.
        - Select the source, define the replication settings, and create the replication session.
- Replication Operations
    - Replication session operations can be performed within Unisphere from two areas.
        - Select a session from the Replication > Sessions page and an option from the More Actions menu.
        - The same More Actions menu options are available from the Replication tab on the storage resource properties page.
    - Replication session operations can be performed from the source or destination systems, depending on the type of replication (asynchronous or synchronous) and the state of the session.
        - Session planned Failover (Synchronous) and Failover with sync (Asynchronous) operations can be performed from the source system when both systems are available.
        - Session Pause,or Sync (Asynchronous) operations are also possible from the replication source.
        - Session Resume operations are available from the source when paused by user or system. The operation is available from the destination system for sessions paused from an unplanned failover operation.
        - Session unplanned Failover operations can be performed from the destination system when the source systems is not available.
        - Session Failback operations are performed from the destination system when the original source system becomes available.
    - NAS servers replicas on the destination might require network interfaces re-configuration to ensure correct operation after a session failover.
    - NAS server replication session operations are also performed on the associated file systems.
- Replica Access
    - Block storage resource replica read-write access is restricted.
        - The access to the data is accomplished through a user snapshot of the resource, and attaching a host to the snapshot.
   -  Replica NAS server production access is inactive
        - The access to the data is accomplished through a user snapshot or by creating a proxy NAS server and associating with the replica NAS server.

