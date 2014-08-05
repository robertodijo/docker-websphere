docker-websphere
================

###Install websphere in Docker
These instructions assume you are installing Websphere 8.5.0 but will work for any version.

####Before you start:
you will need to get the zip files from IBM for websphere fix central : http://www.ibm.com/support/fixcentral/
download the installation files to /tmp/websphere


####Start docker 
 ```docker run -i -t -v /tmp/websphere:/tmpfromhost centos bash ```

####Run up websphere inside docker
 ```
yum -y install unzip
yum -y install gtk2.i686
yum -y install libXtst.i686

mkdir /opt/software/
mkdir /opt/software/IBM
mkdir /opt/software/IBM/installer-package
mkdir /opt/software/IBM/was
mkdir /opt/WebSphere85/

cd /tmpfromhost
unzip InstalMgr1.5.2_LNX_X86_WAS_8.5.zip -d /opt/software/IBM/installer-package
unzip   WAS_V8.5_1_OF_3.zip -d /opt/software/IBM/was
unzip   WAS_V8.5_2_OF_3.zip -d /opt/software/IBM/was
unzip   WAS_V8.5_3_OF_3.zip -d /opt/software/IBM/was

#Install Install Package Manager
cd /opt/software/IBM/installer-package
./installc  -acceptLicense -accessRights admin -installationDirectory "/opt/WebSphere85/IMGR" -dataLocation "/opt/WebSphere85/Imdata" -silent

#Install WAS
cd /opt/WebSphere85/IMGR/eclipse/tools
./imcl listAvailablePackages -repositories /opt/software/IBM/was/repository.config
[get the output and use that string in the next install command e.g. com.ibm.websphere.BASE.v85_8.5.0.20120501_1108]
./imcl -acceptLicense -showProgress install com.ibm.websphere.BASE.v85_8.5.0.20120501_1108 -repositories  /opt/software/IBM/was/repository.config

#create a default profile
cd /opt/IBM/WebSphere/AppServer/bin
./manageprofiles.sh -create -templatePath /opt/IBM/WebSphere/AppServer/profileTemplates/default/
 ```
 
####Build docker image
```docker ps -a```
get the containerId and use it in the next command
```docker commit <containerId> websphere_8_5_0```


