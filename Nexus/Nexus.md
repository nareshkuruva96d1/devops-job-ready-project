# Nexus:
* Nexus is the place where we can store all the artifacts. like war,jar,container images.....
* Compititors for the Nexus are Jfrog, S3,GCS.
* In real world we have to pull and push artifacts to the organizations repositories. like Nexus, Jfrog, S3 and GCS, Org won't allow to pull from the central repo like maven central repo, Docker.hub,.....
* In every Organization we have two teams that are Enterprise team, and Application team.
  * Enterprise will take care of updating the org repo with the latest artifacts and dependancies.
  * Application team will make use of that and used it for application developments.
* If we get any java depency issues, then we need to do some configurations in /opt/nexus/bin/nexus, here we need to uncomment the INSTALL4J_JAVA_HOME_OVERRIDE= and pass the java path.
* We can get the path of the alternative java by using this command `alternatives --config java`
# Create a VM
```bash
gcloud compute instances create nexusserver --zone=us-west4-b --machine-type=e2-medium --create-disk=auto-delete=yes,boot=yes,device-name=nexus,image=projects/centos-cloud/global/images/centos-7-v20221206,mode=rw,size=20
```
# Installing Nexus on Centos:
```bash
# Install Java 1.8
cd /opt
yum install wget -y
wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm

yum install jdk-8u131-linux-x64.rpm -y

# Download Nexus 
# TO get latest version use the below url
# https://help.sonatype.com/repomanager3/product-information/download/download-archives---repository-manager-3
wget https://download.sonatype.com/nexus/3/nexus-3.45.0-01-unix.tar.gz
tar -xzvf nexus-3.45.0-01-unix.tar.gz
mv nexus-3.45.0-01 /opt/nexus

# Create a nexus user.
# Nexus is not advised to run nexus service as a root user.
# So, we are creating a  user called nexus and grant sudo access to manage nexus services.
useradd nexus

#Give the sudo access to nexus user

visudo
nexus ALL=(ALL) NOPASSWD: ALL

#Change the owner and group permissions to /opt/nexus directory
chown -R nexus:nexus /opt/nexus
chmod -R 775 /opt/nexus
#Change the owner and group permissions to /opt/sonatype-work directory.
chown -R nexus:nexus /opt/sonatype-work
chmod -R 775 /opt/sonatype-work

#Open /opt/nexus/bin/nexus.rc file and  uncomment run_as_user parameter and set as nexus user.
vi /opt/nexus/bin/nexus.rc
run_as_user="nexus"

#Create nexus as a service
ln -s /opt/nexus/bin/nexus /etc/init.d/nexus

#Switch as a nexus user and start the nexus service as follows.
sudo su - nexus

#Enable the nexus services
sudo systemctl enable nexus

#Start the nexus service
sudo systemctl start nexus
```
* Access the Nexus server from at http://IPAddress/Hostname:8081/
