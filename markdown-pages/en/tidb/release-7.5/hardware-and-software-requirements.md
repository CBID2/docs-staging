---
title: Software and Hardware Recommendations
summary: Learn the software and hardware recommendations for deploying and running TiDB.
---

# Software and Hardware Recommendations

<!-- Localization note for TiDB:

- English: use distributed SQL, and start to emphasize HTAP
- Chinese: can keep "NewSQL" and emphasize one-stop real-time HTAP ("一栈式实时 HTAP")
- Japanese: use NewSQL because it is well-recognized

-->

As an open-source distributed SQL database with high performance, TiDB can be deployed in the Intel architecture server, ARM architecture server, and major virtualization environments and runs well. TiDB supports most of the major hardware networks and Linux operating systems.

## OS and platform requirements

In v7.5 LTS, TiDB ensures multi-level quality standards for various combinations of operating systems and CPU architectures.

+ For the following combinations of operating systems and CPU architectures, TiDB **provides enterprise-level production quality**, and the product features have been comprehensively and systematically verified:

    <table>
    <thead>
      <tr>
        <th>Operating systems</th>
        <th>Supported CPU architectures</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Red Hat Enterprise Linux 8.4 or a later 8.x version</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td><ul><li>Red Hat Enterprise Linux 7.3 or a later 7.x version</li><li>CentOS 7.3 or a later 7.x version</li></ul></td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>Amazon Linux 2</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>Rocky Linux 9.1 or later</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>Kylin Euler V10 SP1/SP2</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>UnionTech OS (UOS) V20</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>openEuler 22.03 LTS SP1</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
    </tbody>
    </table>

    > **Note:**
    >
    > According to [CentOS Linux EOL](https://www.centos.org/centos-linux-eol/), the upstream support for CentOS Linux 8 ended on December 31, 2021. CentOS Stream 8 continues to be supported by the CentOS organization.

+ For the following combinations of operating systems and CPU architectures, you can compile, build, and deploy TiDB. In addition, you can also use the basic features of OLTP, OLAP, and the data tools. However, TiDB **does not guarantee enterprise-level production quality**:

    <table>
    <thead>
      <tr>
        <th>Operating systems</th>
        <th>Supported CPU architectures</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>macOS 12 (Monterey) or later</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>Oracle Enterprise Linux 7.3 or a later 7.x version</td>
        <td>x86_64</td>
      </tr>
      <tr>
        <td>Ubuntu LTS 18.04 or later</td>
        <td>x86_64</td>
      </tr>
      <tr>
        <td>CentOS 8 Stream</td>
        <td><ul><li>x86_64</li><li>ARM 64</li></ul></td>
      </tr>
      <tr>
        <td>Debian 9 (Stretch) or later</td>
        <td>x86_64</td>
      </tr>
      <tr>
        <td>Fedora 35 or later</td>
        <td>x86_64</td>
      </tr>
      <tr>
        <td>openSUSE Leap later than v15.3 (excluding Tumbleweed)</td>
        <td>x86_64</td>
      </tr>
      <tr>
        <td>SUSE Linux Enterprise Server 15</td>
        <td>x86_64</td>
      </tr>
    </tbody>
    </table>

    > **Note:**
    >
    > - For Oracle Enterprise Linux, TiDB supports the Red Hat Compatible Kernel (RHCK), but does not support the Unbreakable Enterprise Kernel provided by Oracle Enterprise Linux.
    > - Support for Ubuntu 16.04 will be removed in future versions of TiDB. Upgrading to Ubuntu 18.04 or later is strongly recommended.

+ If you are using the 32-bit version of an operating system listed in the preceding two tables, TiDB **is not guaranteed** to be compilable, buildable or deployable on the 32-bit operating system and the corresponding CPU architecture, or TiDB does not actively adapt to the 32-bit operating system.

+ Other operating system versions not mentioned above might work but are not officially supported.

> **Note:**
>
> - For Oracle Enterprise Linux, TiDB supports the Red Hat Compatible Kernel (RHCK) and does not support the Unbreakable Enterprise Kernel provided by Oracle Enterprise Linux.
> - According to [CentOS Linux EOL](https://www.centos.org/centos-linux-eol/), the upstream support for CentOS Linux 8 ended on December 31, 2021. CentOS Stream 8 continues to be supported by the CentOS organization.
> - Support for Ubuntu 16.04 will be removed in future versions of TiDB. Upgrading to Ubuntu 18.04 or later is strongly recommended.
> - If you are using the 32-bit version of an operating system listed in the preceding table, TiDB **is not guaranteed** to be compilable, buildable or deployable on the 32-bit operating system and the corresponding CPU architecture, or TiDB does not actively adapt to the 32-bit operating system.
> - Other operating system versions not mentioned above might work but are not officially supported.

### Libraries required for compiling and running TiDB

|  Libraries required for compiling and running TiDB |  Version   |
|   :---   |   :---   |
|   Golang  |  1.21 or later |
|   Rust    |   nightly-2022-07-31 or later  |
|  GCC      |   7.x      |
|  LLVM     |  13.0 or later  |

Library required for running TiDB: glibc (2.28-151.el8 version)

### Docker image dependencies

The following CPU architectures are supported:

- x86_64. Starting from TiDB v6.6.0, the [x84-64-v2 instruction set](https://developers.redhat.com/blog/2021/01/05/building-red-hat-enterprise-linux-9-for-the-x86-64-v2-microarchitecture-level) is required.
- ARM 64

## Software recommendations

### Control machine

| Software | Version |
| :--- | :--- |
| sshpass | 1.06 or later |
| TiUP | 1.5.0 or later |

> **Note:**
>
> It is required that you [deploy TiUP on the control machine](/production-deployment-using-tiup.md#step-2-deploy-tiup-on-the-control-machine) to operate and manage TiDB clusters.

### Target machines

| Software | Version |
| :--- | :--- |
| sshpass | 1.06 or later |
| numa | 2.0.12 or later |
| tar | any |

## Server recommendations

You can deploy and run TiDB on the 64-bit generic hardware server platform in the Intel x86-64 architecture or on the hardware server platform in the ARM architecture. The requirements and recommendations about server hardware configuration (ignoring the resources occupied by the operating system itself) for development, test, and production environments are as follows:

### Development and test environments

| Component | CPU     | Memory | Local Storage  | Network  | Number of Instances (Minimum Requirement) |
| :------: | :-----: | :-----: | :----------: | :------: | :----------------: |
| TiDB    | 8 core+   | 16 GB+  | [Disk space requirements](#disk-space-requirements) | Gigabit network card | 1 (can be deployed on the same machine with PD)      |
| PD      | 4 core+   | 8 GB+  | SAS, 200 GB+ | Gigabit network card | 1 (can be deployed on the same machine with TiDB)       |
| TiKV    | 8 core+   | 32 GB+  | SAS, 200 GB+ | Gigabit network card | 3       |
| TiFlash | 32 core+  | 64 GB+  | SSD, 200 GB+ | Gigabit network card | 1     |
| TiCDC | 8 core+ | 16 GB+ | SAS, 200 GB+ | Gigabit network card | 1 |

> **Note:**
>
> - In the test environment, the TiDB and PD instances can be deployed on the same server.
> - For performance-related test, do not use low-performance storage and network hardware configuration, in order to guarantee the correctness of the test result.
> - For the TiKV server, it is recommended to use NVMe SSDs to ensure faster reads and writes.
> - If you only want to test and verify the features, follow [Quick Start Guide for TiDB](/quick-start-with-tidb.md) to deploy TiDB on a single machine.
> - Starting from v6.3.0, to deploy TiFlash under the Linux AMD64 architecture, the CPU must support the AVX2 instruction set. Ensure that `cat /proc/cpuinfo | grep avx2` has output. To deploy TiFlash under the Linux ARM64 architecture, the CPU must support the ARMv8 instruction set architecture. Ensure that `cat /proc/cpuinfo | grep 'crc32' | grep 'asimd'` has output. By using the instruction set extensions, TiFlash's vectorization engine can deliver better performance.

### Production environment

| Component | CPU | Memory | Hard Disk Type | Network | Number of Instances (Minimum Requirement) |
| :-----: | :------: | :------: | :------: | :------: | :-----: |
| TiDB  | 16 core+ | 48 GB+ | SSD | 10 Gigabit network card (2 preferred) | 2 |
| PD | 8 core+ | 16 GB+ | SSD | 10 Gigabit network card (2 preferred) | 3 |
| TiKV | 16 core+ | 64 GB+ | SSD | 10 Gigabit network card (2 preferred) | 3 |
| TiFlash | 48 core+ | 128 GB+ | 1 or more SSDs | 10 Gigabit network card (2 preferred) | 2 |
| TiCDC | 16 core+ | 64 GB+ | SSD | 10 Gigabit network card (2 preferred) | 2 |
| Monitor | 8 core+ | 16 GB+ | SAS | Gigabit network card | 1 |

> **Note:**
>
> - In the production environment, the TiDB and PD instances can be deployed on the same server. If you have a higher requirement for performance and reliability, try to deploy them separately.
> - It is strongly recommended to configure TiDB, TiKV, and TiFlash with at least 8 CPU cores each in the production environment. To get better performance, a higher configuration is recommended.
> - It is recommended to keep the size of TiKV hard disk within 4 TB if you are using PCIe SSDs or within 1.5 TB if you are using regular SSDs.

Before you deploy TiFlash, note the following items:

- TiFlash can be [deployed on multiple disks](/tiflash/tiflash-configuration.md#multi-disk-deployment).
- It is recommended to use a high-performance SSD as the first disk of the TiFlash data directory to buffer the real-time replication of TiKV data. The performance of this disk should not be lower than that of TiKV, such as PCIe SSD. The disk capacity should be no less than 10% of the total capacity; otherwise, it might become the bottleneck of this node. You can deploy ordinary SSDs for other disks, but note that a better PCIe SSD brings better performance.
- It is recommended to deploy TiFlash on different nodes from TiKV. If you must deploy TiFlash and TiKV on the same node, increase the number of CPU cores and memory, and try to deploy TiFlash and TiKV on different disks to avoid interfering each other.
- The total capacity of the TiFlash disks is calculated in this way: `the data volume of the entire TiKV cluster to be replicated / the number of TiKV replicas * the number of TiFlash replicas`. For example, if the overall planned capacity of TiKV is 1 TB, the number of TiKV replicas is 3, and the number of TiFlash replicas is 2, then the recommended total capacity of TiFlash is `1024 GB / 3 * 2`. You can replicate only the data of some tables. In such case, determine the TiFlash capacity according to the data volume of the tables to be replicated.

Before you deploy TiCDC, note that it is recommended to deploy TiCDC on PCIe SSD disks larger than 500 GB.

## Network requirements

<!-- Localization note for TiDB:

- English: use distributed SQL, and start to emphasize HTAP
- Chinese: can keep "NewSQL" and emphasize one-stop real-time HTAP ("一栈式实时 HTAP")
- Japanese: use NewSQL because it is well-recognized

-->

As an open-source distributed SQL database, TiDB requires the following network port configuration to run. Based on the TiDB deployment in actual environments, the administrator can open relevant ports in the network side and host side.

| Component | Default Port | Description |
| :--:| :--: | :-- |
| TiDB |  4000  | the communication port for the application and DBA tools |
| TiDB | 10080  | the communication port to report TiDB status |
| TiKV | 20160 | the TiKV communication port |
| TiKV |  20180 | the communication port to report TiKV status |
| PD | 2379 | the communication port between TiDB and PD |
| PD | 2380 | the inter-node communication port within the PD cluster |
| TiFlash | 9000 | the TiFlash TCP service port |
| TiFlash | 3930 | the TiFlash RAFT and Coprocessor service port |
| TiFlash | 20170 |the TiFlash Proxy service port |
| TiFlash | 20292 | the port for Prometheus to pull TiFlash Proxy metrics |
| TiFlash | 8234 | the port for Prometheus to pull TiFlash metrics |
| Pump | 8250 | the Pump communication port |
| Drainer | 8249 | the Drainer communication port |
| TiCDC | 8300 | the TiCDC communication port |
| Monitoring | 9090 | the communication port for the Prometheus service|
| Monitoring | 12020 | the communication port for the NgMonitoring service|
| Node_exporter | 9100 | the communication port to report the system information of every TiDB cluster node |
| Blackbox_exporter | 9115 | the Blackbox_exporter communication port, used to monitor the ports in the TiDB cluster |
| Grafana | 3000 | the port for the external Web monitoring service and client (Browser) access|
| Alertmanager | 9093 | the port for the alert web service |
| Alertmanager | 9094 | the alert communication port |

## Disk space requirements

<table>
<thead>
  <tr>
    <th>Component</th>
    <th>Disk space requirement</th>
    <th>Healthy disk usage</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>TiDB</td>
    <td><ul><li>At least 30 GB for the log disk</li><li>Starting from v6.5.0, Fast Online DDL (controlled by the <a href="https://docs.pingcap.com/tidb/v7.5/system-variables#tidb_ddl_enable_fast_reorg-new-in-v630">tidb_ddl_enable_fast_reorg</a> variable) is enabled by default to accelerate DDL operations, such as adding indexes. If DDL operations involving large objects exist in your application, or you want to use <a href="https://docs.pingcap.com/tidb/v7.5/sql-statement-import-into">IMPORT INTO</a> to import data, it is highly recommended to prepare additional SSD disk space for TiDB (100 GB or more). For detailed configuration instructions, see <a href="https://docs.pingcap.com/tidb/v7.5/check-before-deployment#set-temporary-spaces-for-tidb-instances-recommended">Set a temporary space for a TiDB instance</a></li></ul></td>
    <td>Lower than 90%</td>
  </tr>
  <tr>
    <td>PD</td>
    <td>At least 20 GB for the data disk and for the log disk, respectively</td>
    <td>Lower than 90%</td>
  </tr>
  <tr>
    <td>TiKV</td>
    <td>At least 100 GB for the data disk and for the log disk, respectively</td>
    <td>Lower than 80%</td>
  </tr>
  <tr>
    <td>TiFlash</td>
    <td>At least 100 GB for the data disk and at least 30 GB for the log disk, respectively</td>
    <td>Lower than 80%</td>
  </tr>
  <tr>
    <td>TiUP</td>
    <td><ul><li>Control machine: No more than 1 GB space is required for deploying a TiDB cluster of a single version. The space required increases if TiDB clusters of multiple versions are deployed.</li><li>Deployment servers (machines where the TiDB components run): TiFlash occupies about 700 MB space and other components (such as PD, TiDB, and TiKV) occupy about 200 MB space respectively. During the cluster deployment process, the TiUP cluster requires less than 1 MB of temporary space (<code>/tmp</code> directory) to store temporary files.</li></ul></td>
    <td>N/A</td>
  </tr>
  <tr>
    <td>Ngmonitoring</td>
    <td><ul><li>Conprof: 3 x 1 GB x Number of components (each component occupies about 1 GB per day, 3 days in total) + 20 GB reserved space</li><li>Top SQL: 30 x 50 MB x Number of components (each component occupies about 50 MB per day, 30 days in total)</li><li>Conprof and Top SQL share the reserved space</li></ul></td>
    <td>N/A</td>
  </tr>
</tbody>
</table>

## Web browser requirements

TiDB relies on [Grafana](https://grafana.com/) to provide visualization of database metrics. A recent version of Internet Explorer, Chrome or Firefox with Javascript enabled is sufficient.
