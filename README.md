# graylog deployed on centos7.5
install Graylog

requires:

the graylog contains mongodb,elasticsearch and graylog-server.
version:
mongodb v3.6.9,used to store the configuration of the graylog server.
elasticserch v6.5.4,used to store all the log.
graylog-server v2.5.1 
java  v1.8--
Graylog 2.5.x should be used with Elasticsearch 6.x

disable the selinux  and firewalld.

1, create a user,because the elasticsearch need it 
#useradd graylog 
2, MongoDB, 
add the repository file /etc/yum.repos.d/mongodb-org-3.6.repo with the following contents:

  [mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc

After that, install the latest release of MongoDB 
 #sudo yum install -y mongodb-org
 
 Additionally, run these last steps to start MongoDB during the operating system’s boot and start it right away:
 
$ sudo chkconfig --add mongod
$ sudo systemctl daemon-reload
$ sudo systemctl enable mongod.service
$ sudo systemctl start mongod.service

3,elasticsearch 

first install the Elastic GPG keyz;
 
 #rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

 add the repository file /etc/yum.repos.d/elasticsearch.repo with the following contents:
 
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md

After that, install the latest release of elasticsearch  .
#sudo yum install elasticsearch

edit the configure file of elasticsearch (/etc/elasticsearch/elasticsearch.yml) and set the cluster name to graylog.
cluster.name: graylog
run these last steps to start elasticsearch during the operating system’s boot and start it right away:

$ sudo chkconfig --add elasticsearch
$ sudo systemctl daemon-reload
$ sudo systemctl enable elasticsearch.service
$ sudo systemctl restart elasticsearch.service


4,graylog 

#sudo rpm -Uvh https://packages.graylog2.org/repo/packages/graylog-2.5-repository_latest.rpm
#sudo yum install graylog-server

after installed,edit the configure file (/etc/graylog/server/server.conf),add the key (password_secret) and (root_password_sha2),if not, the Graylog will not be started.
the following conmand can get the (root_password_sha2),just run it.
#echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1

then,set the (rest_listen_uri),(root_timezone) and (web_listen_uri) like this:

root_timezone = Asia/Shanghai
rest_listen_uri = http://103.45.111.178:9000/api/
web_listen_uri = http://103.45.111.178:9000/

attention:"103.45.111.178" is the grayserver's ip address,it shoud be connected by other server.

at last:
$ sudo chkconfig --add graylog-server
$ sudo systemctl daemon-reload
$ sudo systemctl enable graylog-server.service
$ sudo systemctl start graylog-server.service


now everything is ok,you can login it by the url:http://103.45.111.178:9000/。
the defalut user is :admin

somthing about the grayserver:

the "inputs" ：likes the door of the graylog server,it receives all the logs from other server
the "Collectors " : configure the client,and add the tags
the "streams":by the rule ,you can filter the logs as you want.




 
