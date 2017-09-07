# Hadoop Remote Client Config
A Hadoop client setup is needed if a seperate ETL tool or data ingestion mechanism is used to connect to a remote cluster.  The client does not store any data rather is points to a remote cluster where the data is persisted. Find below a sample reference architecture to how the eventual setup will look like.

 ![alt text](https://github.com/ICFI/hadoop_client_config/blob/master/HadoopClientConfig.PNG "Logo Title Text 1")

Listed below are steps to get a client installed on a Windows environment.  The same steps can also be applied to a non-windows environment.  

-	Pre-requisites
    - Have Java JDK or JRE installed.
    - JAVA_HOME variable is set correctly in the environment
    - Setting the java environment correctly
        - JAVA_HOME should be set with 8.3 naming convention.  Avoid using the "Program Files" folder which is not liked. This could leave JAVA_HOME variable to be incorrect.
        - Reccomended is to have a localized version of JAVA that can be used for Hadoop purposes.  Example: If C:\Hadoop is the location where the Hadoop deployment will be place:  Create the JRE/JDK package under C:\Hadoop itself and also set that location for JAVA_HOME setting in \hadoop\etc\hadoop\hadoop-env.cmd file

-	Download Hadoop packages from apache Hadoop website. (At the time of this document, version 2.7.1 was current)
    - http://hadoop.apache.org/releases.html#Download 
    - Download the binaries for the Win OS environment.  If downloading the source, addition compilation steps will need to be followed and those are not covered in this document.  
    - Download the source only if additional customizations are needed to the core packages.  Assuming this would one off requirements and it needs to be committed back to comply with licensing terms.
    - Unzip the binaries in a local directory: C:\hadoop\<version>

- The setup can be used as a single node or a client or a cluster. If it’s a single node there is a separate set of configs needed that includes storage space. Creation of a cluster also requires few more configs that will need to be repeated on the other machines that will make up the cluster.  In this case, the client will only act as a mechanism to connect to a remote cluster hence needing configuration pointing to the remote cluster.

- Set the cluster locations in core-site.xml and mapred-site.xml   (can be found in the C:\hadoop\hadoop-2.7.1\etc\hadoop )

    - Recommended method is to go the cluster, download the config and find the namenode IP/port and mapred ip/port.
    - If using a cluster manager like Ambari, navigate to the HDFS configuration tab to find the info
    
- Modify core-site.xml.  It may look like this:

```
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://172.25.100.161:8020</value>   #-- this is the IP for the named node
    <final>true</final>
</property>
```

- Modify mapred-site.xml

```
<property>
    <name>mapred.job.tracker</name>
    <value>172.25.100.161:8021</value>    #this is the IP for the mapreduce service is running
</property>
```

-	Download winutils.exe from Hadoop or HDP site, and place it in the hadoop/bin dir.
    - URL from the Hadoop master points to https://github.com/steveloughran/winutils .  Either use git clone or download the zip and place the contents in the Hadoop/bin directory.
    - Also ensure Hadoop distribution is 2.4 and above (earlier ones had issues)
    
- From Windows set cross platform setting in MapRed config

```
<property>
    <name>mapreduce.app-submission.cross-platform</name>
    <value>true</value>
</property>

```

-	Setting environment variables 
```
    HADOOP_USER_NAME=hdfs
    HADOOP_HOME=C:\hadoop\hadoop-2.7.1\
    PATH=%PATH%;%HADOOP_HOME/bin;
```
(The above settings is all done using Control Panel>System and Security>System>Advanced System Settings>Environment Variables)


-	Add the namednode host name to the /etc/hosts file on Windows (%systemroot%\system32\drivers\etc\hosts)
    - 172.25.100.161 sandbox.hadoop.com
    (This is required for the HIVE connectivity as it does not like IP addresses)
-	Perform a quick test to see if the setup is working fine:
    - Issue a “Hadoop fs –ls /tmp” command

