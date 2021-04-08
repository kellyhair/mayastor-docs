# Prerequisites

### **General**

Each Mayastor Node \(MSN\), that is each cluster worker node which will host an instance of a Mayastor pod, must have these resources _free and available_ for use by Mayastor:

* **Two  x86-64 CPU cores with SSE4.2 instruction support**:
  * Intel Nehalem processor \(march=nehalem\) and newer
  * AMD Bulldozer processor and newer
* **4GiB of RAM**
* HugePage support

  * A minimum of **1GiB of** **2MiB-sized** **huge pages**

{% hint style="info" %}
Instances of the Mayastor pod _must_ be run in privileged mode
{% endhint %}

#### Linux commands to ensure nodes meet the requirements

This section highlights the commands needed to ensure the Kuberetes nodes in the cluster are supported. 


To confirm SSE4.2 support:

```
lshw -class processor | grep -w sse2
```

If the `grep` command output includes `sse2` then your nodes will be supported.

To confirm enough memory is available on the node: 

```
cat /proc/meminfo
```

If you have atleast 4,000,000 kB available then proceed to the next check for HugePage support.

To confirm HugePage support: 

First step, ensure that 1GB page support is enabled for your CPU:

```
cat /proc/cpuinfo | grep flags | head -n 1 | grep -w "pdbe1gb"
```

If the output confirms that huge pages are supported then continue on the second step.

Second step, check for Hugepagesupport in memory: 

```
grep Hugepagesize /proc/meminfo
```

If the output confirms that `Hugepagesize` is set to `2048 kB` then you are all set. 

However, if the target system has a lower value then.. 

**ADD COMMANDS FOR THIS***

### Node Count

As long as the resource prerequisites are met, Mayastor can deployed to a cluster with just a single worker node.  However note that in order to evaluate the synchronous replication feature \(N+1 mirroring\), the number of worker nodes to which Mayastor is deployed should be no less than the desired replication factor.  E.g. three-way mirroring of Persistent Volumes \(PV\) would require Mayastor to be deployed to a minimum of three worker nodes.

### Transport Protocols

Mayastor supports the export and mounting of a Persistent Volume over either NVMe-oF TCP or iSCSI \(configured as a parameter of the PV's underlying StorageClass\).  Worker node\(s\) on which a PV is to be mounted must have the requisite initiator support installed and configured for the protocol in use.

#### iSCSI

The iSCSI client should be installed and correctly configured as per [this guide](https://docs.openebs.io/docs/next/prerequisites.html).

#### NVMe-oF

In order to reliably mount application PVs  over NVMe-oF TCP, a worker node's kernel version must be 5.3 or later.  Verify that the `nvme-tcp` module is loaded and if necessary, load it.



