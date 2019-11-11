# cyclecloud-gridengine-java
Open GridEngine compiled with support for Java DRMAA API

CycleCloud GridEngine Project
This project adds Java support to the Open Grid Engine supplied with Azure CycleCloud

## Prerequisites
This example will use the sge-2011.11 version specifically compiled with support for Java.  
1. Use the Java compiled files OGE files named as follows (NOTE:  do not change the file names):  
		    
		    sge-2011.11j-64.tgz  
		    sge-2011.11j-common.tgz
		    
2. The cyclecloud cli must be configured. Documentation is available here:  
  [https://docs.microsoft.com/en-us/azure/cyclecloud/install-cyclecloud-cli](https://docs.microsoft.com/en-us/azure/cyclecloud/install-cyclecloud-cli)


## Copy the binaries into the cloud locker

Using the CLI, determine the url of the cloud locker for your installation, then use the pogo put command (installed with cyclecloud cli) to upload the installers to the locker.
```
$ cyclecloud locker list
azure-storage (az://myprojectwestus2/cyclecloud)
$ pogo put sge-2011.11j-64.tgz az://myprojectwestus2/cyclecloud/cache/projects/gridengine/blobs/
Copied: az://myprojectwestus2/cyclecloud/cache/projects/gridengine/blobs/sge-2011.11j-64.tgz 
Processed:   1 | Transferred:   1 | Deleted:   0 | Failures:   0 | Average rate: 5.43 MBps  
$ pogo put sge-2011.11j-common.tgz az://myprojectwestus2/cyclecloud/cache/projects/gridengine/blobs/
Copied: az://myprojectwestus2/cyclecloud/cache/projects/gridengine/blobs/sge-2011.11j-common.tgz
Processed:   1 | Transferred:   1 | Deleted:   0 | Failures:   0 | Average rate: 6.04 MBps  
```

## Add OGE-Java configs to the cluster template
Add the following lines to your gridengine.txt template and modify it to use the OGE-Java installers instead of the default.
NOTE: The details in the configuration, particularly version, should match the installer file name.
```
[[[configuration]]]  
    gridengine.make = sge  
    gridengine.version = 2011.11j  
    gridengine.root = /sched/sge/sge-2011.11j
```  
These configs will override the default gridengine version and installation location, as the cluster starts. It is not safe to move off of the /sched as it's a specifically shared nfs location in the cluster.


## Import the cluster template file
Using the cyclecloud cli, import a cluster template from the modified gridengine.txt cluster template file. 

	cyclecloud import_cluster OGE-Java -c 'grid engine' -f gridengine.txt -t --force

Similar to this tutorial in the documentation, new OGE-Java cluster type is now available in the Create Cluster menu in the UI.
Configure and create the cluster in the UI, save it, and start it.

## Verify GridEngine version
As an example, start a cluster in CycleCloud that has been configured and is named "test-uge" or whatever you want to name it. When the master node reaches the Started state (green), log into the node with the cyclecloud connect command.
	cyclecloud connect master -c test-uge`

```Last login: Tue Jan 29 20:37:14 2019 

 __        __  |    ___       __  |    __         __|
(___ (__| (___ |_, (__/_     (___ |_, (__) (__(_ (__|
        |

Cluster: test-uge
Version: 7.7.5

Then check the grid engine version with qstat
qstat -h
OGS/GE 2011.11p1
usage: qstat [options]```
