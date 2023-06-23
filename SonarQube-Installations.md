## Installing SonarQube:
## Create a server on GCP:
```bash
gcloud compute instances create sonarqube  --zone=us-west4-b --machine-type=e2-medium  --create-disk=auto-delete=yes,boot=yes,device-name=tomcatnew,image=projects/centos-cloud/global/images/centos-7-v20230615,mode=rw,size=20
```
## Install Openjdk-17 and mvn 3.8.8 on Centos:
```bash
# https://techviewleo.com/install-java-openjdk-on-rocky-linux-centos/
# Install Openjdk 17
sudo yum -y install wget curl
wget https://download.java.net/java/GA/jdk17.0.2/dfd4a8d0985749f896bed50d7138ee7f/8/GPL/openjdk-17.0.2_linux-x64_bin.tar.gz
tar xvf openjdk-17.0.2_linux-x64_bin.tar.gz
sudo mv jdk-17.0.2/ /opt/jdk-17/
vim ~/.bashrc
  export JAVA_HOME=/opt/jdk-17
  export PATH=$PATH:$JAVA_HOME/bin
source ~/.bashrc
echo $JAVA_HOME
java --version

# Install Maven 3.8.8
cd /opt/
# wget https://dlcdn.apache.org/maven/maven-3/3.8.7/binaries/apache-maven-3.8.7-bin.tar.gz
wget https://dlcdn.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.tar.gz
tar -xzvf apache-maven-3.8.8-bin.tar.gz
vi ~/.bashrc
  export PATH=$PATH:/opt/apache-maven-3.8.8/bin
  export M2_HOME=/opt/apache-maven-3.8.8
source ~/.bashrc
mvn --version

# For All Users
---------------------- 
vi /etc/profile
  export JAVA_HOME=/opt/jdk-17
  export PATH=$PATH:$JAVA_HOME/bin
  export PATH=$PATH:/opt/apache-maven-3.8.8/bin
  export M2_HOME=/opt/apache-maven-3.8.8

source /etc/profile
mvn --version
```
## Installing SonarQube:
* For historical downloads [Refer Here](https://www.sonarsource.com/products/sonarqube/downloads/historical-downloads/)
  ```bash
  wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.7.1.62043.zip
  yum install unzip -y
  mv sonarqube-9.7.1.62043 sonarqube-9.7
  ```
* Sonarqube will not be executed with `root` user
* we need to create user called as `sonar`.
```bash
useradd sonar
# visudo
sonar         ALL=(ALL)       NOPASSWD: ALL
chown -R sonar:sonar /opt/sonarqube-9.7
cd sonarqube-9.7/bin/linux-x86-64/
 * sonar.sh
 * need to give executable permissions
cd /opt/
chmod -R 775 /opt/sonarqube-9.7
su - sonar
/opt/sonarqube-9.7/bin/linux-x86-64
sh sonar.sh start
```
* After executing the sonar.sh file, now we can able to access the sonarqube from the web, by passing the public IP with `9000` port.
* By default sonar will provide the login credentials for sonar are admin and admin, now afetr login to the sonar we have to change the password.
* companies sonarqube will be integrated with our Msid's
* <img width="941" alt="image" src="https://github.com/nareshkuruva96d1/devops-job-ready-project/assets/135315223/730ff3e0-3ec8-47ec-aa4d-7c14eb2238b5">
* After login to the SonarQube we will get the above Page. Here we go with the any devops platform tools or we can go with the manuval project creation.
* If we are using the maven we can also add some arguments in the properties section of the pom.xml file. like
```bash
    <sonar.host.url>http://34.16.140.226:9000/</sonar.host.url>
    <sonar.login>admin</sonar.login>
    <sonar.password>knaresh</sonar.password>
```
* And while packaging we have to inform maven to pass the src code through the sonarqube for that we have to execute this command
```bash
mvn sonar:sonar 
```
* It works but it is not the correct way, we do not pass the `user name` and `password` in the `pom.xml` it is the security issue.
* Instad of that we have to create the token for that user squ_e67c32d50a494918967379c6d408eaac78603b11.
* Afetr generating the token we have to place this in the `pom.xml`
```bash
<sonar.host.url>http://34.16.140.226:9000/</sonar.host.url>
<sonar.login>squ_e67c32d50a494918967379c6d408eaac78603b11</sonar.login>
```
* After saving the file verify is that working fine or not `mvn sonar:sonar`
## Create the dommy project:
* <img width="944" alt="image" src="https://github.com/nareshkuruva96d1/devops-job-ready-project/assets/135315223/3c8fa04d-4619-41ec-8ff3-5a4fd0f09e7e">
* select locall
* <img width="940" alt="image" src="https://github.com/nareshkuruva96d1/devops-job-ready-project/assets/135315223/c72a07c2-7925-42c1-abfe-6f7a3f819e5c">
* Select the existing token and pass that here.
* <img width="943" alt="image" src="https://github.com/nareshkuruva96d1/devops-job-ready-project/assets/135315223/eeec09eb-204a-4537-a483-96d3b9b40ce1">
* Here select the build which one you are using, and it will give you the arguments related to that build tool.
* <img width="938" alt="image" src="https://github.com/nareshkuruva96d1/devops-job-ready-project/assets/135315223/f7fa8c4c-6d7b-452e-8133-ac3eb84ed0aa">
* if we pass the below arguments in the maven it automatically create the project in the sonarqube. Because it is not the good practice to pass the token in the `pom.xml`, so instead of that we can pass the below commands
```bash
mvn clean verify sonar:sonar \
  -Dsonar.projectKey=test-project-1 \
  -Dsonar.host.url=http://34.16.140.226:9000 \
  -Dsonar.login=squ_e67c32d50a494918967379c6d408eaac78603b11
```
* if we pass the arguments like this without passing the projectkey then it will automatically takes that from the pom.xml




