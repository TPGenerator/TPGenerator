# TPGenerator: Lauca

## Quick Start for TiDB with PingCAP

- We can simply use the following commands to simulate data and workloads.  The TiDB example in [LaucaExample](LaucaExample). Here, we provide a configuration file about TiDB in both [LaucaProduction](./LaucaExample/Production/lauca-tidb.conf) and [LaucaTesting](./LaucaExample/Testing/lauca-tidb.conf)， and according jar files in [LaucaProduction.jar](./LaucaExample/Prodution/LaucaProduction.jar)  and [LaucaTesting.jar](./LaucaExample/Testing/LaucaTesting.jar).

```shell

cd LaucaExample
cd Production
# this step will automatically catpured the data characteristics and then create dataCharacteristicSaveFile.obj in testdata file
java -jar LaucaProduction.jar ./lauca-tidb.conf --getDataCharacteristics
# this step will automatically catpured the workload characteristics(transaction logic and data access distribution) and then create txLogicSaveFile.obj  and distributionSaveFile.obj in testdata file
java -jar LaucaProduction.jar ./lauca-tidb.conf --getWorkloadCharacteristics

#open anothor terminal
cd LaucaExample
cd Testing
# this step will automatically generate data in testdatab/data according to the dataCharacteristicSaveFile.obj
java -jar LaucaTesting.jar ./lauca-tidb.conf --geneSyntheticDatabase
# this step will automatically load data from testdatab/data into TiDB database which is configurated in lauca-tidb.conf.
java -jar LaucaTesting.jar ./lauca-tidb.conf --loadSyntheticDatabase
# this step will automatically generate workload into the TiDB database according dataCharacteristicSaveFile.obj, txLogicSaveFile.obj  and distributionSaveFile.obj.
java -jar LaucaTesting.jar ./lauca-tidb.conf --geneSyntheticWorkload
```



## Example to get workload trace

- We can get the workload trace via skywalking, the link is https://github.com/apache/skywalking
- The application can print workload traces through java agent. Here we use oltp-bench which generates TPC-C as our application side. Users can use the following step to load the database and print the workload trace.

```
cd oltpbench
# configurate the database info
vim config/tpcc_config_tidb.xml
# load the database
./oltpbenchmark -b tpcc -c config/tpcc_config_tidb.xml --create=true --load=true
# generate the workload into databases and print the workload trace
./oltpbenchmarkSkywalking -b tpcc -c config/tpcc_config_tidb.xml --execute=true
```

- But we also provide a workload trace about TPC-C with 20 warehouses, 20 connections and 100 second running time available in [workload trace](./lauca-log)



## Overview of Program Files

- In the *LaucaExample* folder, there are three subfolders, namely, the production environment, the testing environment and testdata. In the production environment, the basic data feature *dataCharacteristicSaveFile.obj*, the transaction logic feature *txLogicSaveFile.obj* and the workload access distribution feature *distributionSaveFile.obj* are generated in *testdata*. The testing environment generates a synthetic database using the basic data features,  which create *tables* file in *testdata* and are then used to generate the database through its second step. In addition, the testing environment generates simulated workloads using transaction logic and workload access distribution.  *LaucaProduction.jar*  is in the Production folder and *LaucaTesting.jar* is in the Testing folder. The former is responsible for extracting the data features and workloads features, while the latter is responsible for generating the simulated database and simulated workloads.

```tree
LaucaExample/
├── Production
│   ├── lauca-tidb.conf
│   ├── LaucaProduction.jar
│   └── log4j.properties
├── testdata
│   ├── dataCharacteristicSaveFile.obj
│   ├── distributionSaveFile.obj
│   ├── tables
│   └── txLogicSaveFile.obj
└── Testing
    ├── lauca-tidb.conf
    ├── LaucaTesting.jar
    └── log4j.properties

```
