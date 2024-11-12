# Jenkins_Files
1 . Add Vault server and SonarQube server in Jenkins System configuration

## Setup vault 
1. Configure SonarQube token in vault
```bash

vault kv put secret/data/sonarcred SONAR_PASS=xxxxx  SONAR_USER=xxxxxxx  SONAR_TOKEN=xxxxxxxxxxx
```
```bash
root@haproxy-lb:~# vault kv get secret/data/sonarcred
======= Secret Path =======
secret/data/data/sonarcred

======= Metadata =======
Key                Value
---                -----
created_time       2024-11-11T13:01:56.446186622Z
custom_metadata    <nil>
deletion_time      n/a
destroyed          false
version            2

======= Data =======
Key            Value
---            -----
SONAR_PASS     xxxx
SONAR_TOKEN    xxxx
SONAR_USER     xxxx
root@haproxy-lb:~# 
```


2. Setup SonarQube Webhooks with Jenkins server

```
Go to SonarQube > Administration > Configureation > Webhooks > Create 
Name: xxxx
URL: http://192.168.1.120:8070/sonarqube-webhook
```

3. Store Nexus credential in Vault 
```bash
vault kv put secret/data/nexuscred NEXUS_PASS=xxxxxx NEXUS_USER=admin
```


3. Configure pom.xml and settings.xml to access nexus repo to upload build
- pom.xml
```xml
<distributionManagement>
    		<repository>
        		<id>pgr-nexus-repo-release</id> <!-- This id should match the 'id' in your settings.xml -->
        		<url>http://192.168.1.130:8081/repository/pgr-maven-release/</url> <!-- Release repository URL -->
    		</repository>
    		<snapshotRepository>
        		<id>pgr-nexus-repo-snapshot</id> <!-- Optional: For snapshot builds -->
        		<url>http://192.168.1.130:8081/repository/pgr-maven-snapshot/</url>
    		</snapshotRepository>
	</distributionManagement>
```
- setting.xml in maven server
cd /etc/maven/settings.xml
```xml
<servers>
    <server>
        <id>pgr-nexus-repo-snapshot</id>
        <username>xxx</username>
        <password>xxx</password>
    </server>
</servers>
```


4. Docker Registry vautl setup
