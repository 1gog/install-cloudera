# install-cloudera





### stage
```bash
cat <<EOF >> /etc/hosts
21.0.1.26 cdh-name.stage.dev
21.0.1.27 cdh-node-01.stage.dev
21.0.1.28 cdh-node-02.stage.dev
EOF
```
repeate command on all host 
cdh-name.stage.dev
cdh-node-01.stage.dev
cdh-node-02.stage.dev


### open port
```bash
firewall-cmd --permanent --zone=public --add-port=22/tcp
firewall-cmd --reload
```
#### for cloudera cdh-name.stage.dev
```
firewall-cmd --permanent --add-port=7180/tcp --zone=public
firewall-cmd --reload
```



# update system
```bash
yum update -y
```
### install java jdk
```bash
yum install -y java-1.8.0-openjdk
```

### get repo for you system
https://www.cloudera.com/documentation/enterprise/release-notes/topics/cm_vd.html


### install repo for centos 7 x64
```bash
cd /etc/yum.repos.d/
wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo
```

### list repo
```bash
yum repolist
```

### install cloudera manager server package on host cdh-name.stage.dev 
```bash
yum install -y cloudera-manager-daemons cloudera-manager-server cloudera-manager-agent
```

### install on all host (cdh-node-01, cdh-node-02) 
```bash
yum install -y cloudera-manager-daemons cloudera-manager-agent
```



### install and init postgres
```bash
yum install -y postgresql-server
su -l postgres 
initdb
exit
```

### start serice 
```bash
systemctl start postgresql
```

### configure postgresql
```bash
vi /var/lib/pgsql/data/postgresql.conf
```

#### find listen_addresses and replace to
```
listen_addresses='*'
```

#### find shared_buffers  and replace to
```
shared_buffers = 256MB
```

#### add access from network
```
echo 'host   all  cloudera  21.0.1.0/24  md5' >> /var/lib/pgsql/data/pg_hba.conf
```

#### restart postgresql
```
systemctl restart postgresql
```

#### login to postgresql as admin
```
su -l postgres
psql
```

#### create user cloudera
```
create user cloudera with encrypted password 'mypass';
```
#### create database 
```
create database cloudera;
```

#### grant privileged 
```
grant all privileges on database cloudera to cloudera;
```


#### for sqoop
```
CREATE ROLE sqoop LOGIN ENCRYPTED PASSWORD 'sqoop' NOSUPERUSER INHERIT CREATEDB NOCREATEROLE;
```
#### create database
```
CREATE DATABASE "sqoop" WITH OWNER = sqoop ENCODING = 'UTF8'
 TABLESPACE = pg_default
 LC_COLLATE = 'en_US.UTF8'
 LC_CTYPE = 'en_US.UTF8'
 CONNECTION LIMIT = -1;
```

#### download jar for postgres
```
cd /usr/lib/java-ext
wget https://jdbc.postgresql.org/download/postgresql-42.2.6.jar
```

#### edit db properties 
```
vi /etc/cloudera-scm-server/db.properties
```


#### replace
```
com.cloudera.cmf.db.type=postgresql
com.cloudera.cmf.db.host=21.0.1.26
com.cloudera.cmf.db.name=cloudera
com.cloudera.cmf.db.user=cloudera
com.cloudera.cmf.db.password=mypass
```















### start service cloudera-manager-server on host cdh-name.stage.dev
```bash
systemctl enable cloudera-scm-server 
systemctl start cloudera-scm-server
```

### start service cloudera-manager-agent on host cdh-node-01.stage.dev, cdh-node-02.stage.dev
```bash
systemctl enable cloudera-scm-agent
systemctl start cloudera-scm-agent
```





