# OAP Developer Guide

This document contains the instructions & scripts on installing necessary dependencies and building OAP modules. 
You can get more detailed information from OAP each module below.

* [SQL Index and Data Source Cache](https://github.com/oap-project/sql-ds-cache/blob/v1.1.1-spark-3.1.1/docs/Developer-Guide.md)
* [PMem Common](https://github.com/oap-project/pmem-common/tree/v1.1.1-spark-3.1.1)
* [PMem Spill](https://github.com/oap-project/pmem-spill/tree/v1.1.1-spark-3.1.1)
* [PMem Shuffle](https://github.com/oap-project/pmem-shuffle/tree/v1.1.1-spark-3.1.1#5-install-dependencies-for-pmem-shuffle)
* [Remote Shuffle](https://github.com/oap-project/remote-shuffle/tree/v1.1.1-spark-3.1.1)
* [OAP MLlib](https://github.com/oap-project/oap-mllib/tree/v1.1.1-spark-3.1.1)
* [Native SQL Engine](https://github.com/oap-project/native-sql-engine/tree/v1.1.1-spark-3.1.1)

## Building OAP

### Prerequisites

We provide scripts to help automatically install dependencies required, please change to **root** user and run:

```
# git clone -b <tag-version> https://github.com/oap-project/oap-tools.git
# cd oap-tools
# sh dev/install-compile-time-dependencies.sh
```
*Note*: oap-tools tag version `v1.1.1-spark-3.1.1` corresponds to  all OAP modules' tag version `v1.1.1-spark-3.1.1`.

Then the dependencies below will be installed:

* [Cmake](https://cmake.org/install/)
* [GCC > 7](https://gcc.gnu.org/wiki/InstallingGCC)
* [Memkind](https://github.com/memkind/memkind/tree/v1.10.1)
* [Vmemcache](https://github.com/pmem/vmemcache)
* [HPNL](https://github.com/Intel-bigdata/HPNL)
* [PMDK](https://github.com/pmem/pmdk)  
* [OneAPI](https://software.intel.com/content/www/us/en/develop/tools/oneapi.html)
* [Arrow](https://github.com/oap-project/arrow/tree/arrow-4.0.0-oap-1.1.1)
* [LLVM](https://llvm.org/) 

Run the following command to learn more.

```
# sh dev/scripts/prepare_oap_env.sh --help
```

Run the following command to automatically install specific dependency such as Maven.

```
# sh dev/scripts/prepare_oap_env.sh --prepare_maven
```

- **Requirements for Shuffle Remote PMem Extension**  
If enable Shuffle Remote PMem extension with RDMA, you can refer to [PMem Shuffle](https://github.com/oap-project/pmem-shuffle) to configure and validate RDMA in advance.

### Building

OAP is built with [Apache Maven](http://maven.apache.org/) and Oracle Java 8.

To build OAP package, run command below then you can find a tarball named `oap-$VERSION-bin-spark-$VERSION.tar.gz` under directory `$OAP_TOOLS_HOME/dev/release-package `.
```
$ sh $OAP_TOOLS_HOME/dev/compile-oap.sh
```

Building specified OAP Module, such as `sql-ds-cache`, run:
```
$ sh $OAP_TOOLS_HOME/dev/compile-oap.sh --sql-ds-cache
```

## Enabling NUMA binding for PMem in Spark

### Rebuilding Spark packages with NUMA binding patch 

When using PMem as a cache medium apply the [NUMA](https://www.kernel.org/doc/html/v4.18/vm/numa.html) binding patch [numa-binding-spark-3.1.1.patch](./numa-binding-spark-3.1.1.patch) to Spark source code for best performance.

1. Download src for [Spark-3.1.1](https://archive.apache.org/dist/spark/spark-3.1.1/spark-3.1.1.tgz) and clone the src from github.

2. Apply this patch and [rebuild](https://spark.apache.org/docs/latest/building-spark.html) the Spark package.

```
git apply numa-binding-spark-3.1.1.patch
```

3. Add these configuration items to the Spark configuration file $SPARK_HOME/conf/spark-defaults.conf to enable NUMA binding.

```
spark.yarn.numa.enabled true 
```
