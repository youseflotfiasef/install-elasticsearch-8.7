# install-elasticsearch-8.7
##install elasticsearch-8.7.0 with kibana and logstash  
Install elasticsearch 8.7.0  on ubuntu 20.04:
1- download form link below 
    $ wget -c https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.7.0-amd64.deb
Notes: 
wget -c  when problem internet with switch -c to continue 

2- next step  download and  install dpkg 
$ dpkg -i elasticsearch-8.7.0-amd64.deb
3- after that  install go to path /etc/elasticsearch 
$ vim /etc/elasticsearch/elasticsearch.yml
#
```
cluster.name: xon
node.name: elk-node1
path.data: /opt/elasticsearch/data
path.logs: /opt/elasticsearch/log
#network.host: 192.168.0.1
#http.port: 9200
discovery.seed_hosts: ["elk-node1","elk-node2","elk-node3","elk-node4"]
#action.destructive_requires_name: false
xpack.security.enabled: true
xpack.security.enrollment.enabled: true
xpack.security.http.ssl:
 enabled: false
 keystore.path: certs/http.p12
xpack.security.transport.ssl:
 enabled: true
 verification_mode: certificate
 keystore.path: elastic-certificates.p12
 truststore.path: elastic-certificates.p12
cluster.initial_master_nodes: ["elk-node1","elk-node2","elk-node3"]
http.host: 0.0.0.0
transport.host: 0.0.0.0
```
#
4- Next step create dir below 
    $ mkdir -p /opt/elasticsearch/log ; mkdir -p /opt/elasticsearch/data ; mkdir -p /opt/elasticsearch/tmp
5- another step set permission file and directory 
    $ chmod -R 777 /opt/elasticsearch/
6- step 6 change config defaults  jvm.options config 
    $ vim /etc/elasticsearch/jvm.options
#
```
cluster.name: xon
node.name: elk-node1
path.data: /opt/elasticsearch/data
path.logs: /opt/elasticsearch/log
#network.host: 192.168.0.1
#http.port: 9200
discovery.seed_hosts: ["elk-node1","elk-node2","elk-node3","elk-node4"]
#action.destructive_requires_name: false
# --------------------------------------------------------------------------------
xpack.security.enabled: true
xpack.security.enrollment.enabled: true
xpack.security.http.ssl:
 enabled: false
 keystore.path: certs/http.p12
xpack.security.transport.ssl:
 enabled: true
 verification_mode: certificate
 keystore.path: elastic-certificates.p12
 truststore.path: elastic-certificates.p12
cluster.initial_master_nodes: ["elk-node1","elk-node2","elk-node3"]
http.host: 0.0.0.0
transport.host: 0.0.0.0
#----------------------- END SECURITY AUTO CONFIGURATION -------------------------
root@elk-node1:/home/y-lotfi# cat /etc/elasticsearch/jvm.options
-Xms16g
-Xmx16g
-XX:+UseG1GC
-Djava.io.tmpdir=/opt/elasticsearch/tmp
-XX:+HeapDumpOnOutOfMemoryError
-XX:+ExitOnOutOfMemoryError
-XX:HeapDumpPath=/var/lib/elasticsearch
-XX:ErrorFile=/var/log/elasticsearch/hs_err_pid%p.log
-Xlog:gc*,gc+age=trace,safepoint:file=/var/log/elasticsearch/gc.log:utctime,level,pid,tags:filecount=32,filesize=64m

```
Nots : 
For crack ealsticsearch  you copy file *.jar to directory xpack such as below 
    $ cp x-pack-core-8.7.0.jar /usr/share/elasticsearch/modules/x-pack-core/x-pack-core-8.7.0.jar 
7- finally add rule in iptable 
    $ vim /etc/iptables/rules.v4
8-  in step name is security step and set password 
     $  /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic 
     $  /usr/share/elasticsearch/bin/elasticsearch-certutil ca
     $  locate elastic-stack-ca.p12
     $  updatedb
     $  /usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca /usr/share/elasticsearch/elastic-stack-ca.p12
     $  updatedb
     $  locate elastic-certificates.p12
     $  cp /usr/share/elasticsearch/elastic-certificates.p12 /etc/elasticsearch/
     $  cat /usr/share/elasticsearch/elastic-certificates.p12
     $ cp /usr/share/elasticsearch/elastic-certificates.p12 /home/users/
     $ chmod 777 elastic-certificates.p12

9- The finally start and test install elasticsearch 
     $ systemctl daemon-reload 
     $ systemctl enable –now  elasticsearch
     $ systemctl status elasticsearch 
     $ tail -n 200 /opt/elasticsearch/log/elasticsearch.log
     $ netstat -npl | grep 9200
     $ curl -XGET "http://127.0.0.1:9200/_cat/nodes?v"

10- install kibana 
    $ wget -c https://artifacts.elastic.co/downloads/kibana/kibana-8.7.1-amd64.deb
11- Add for kibana: 
     $  /usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
     $  /usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
12- Create keystore: 
     $  /usr/share/kibana/bin/kibana-keystore create
     $  /usr/share/kibana/bin/kibana-keystore add elasticsearch.password
13- Create password kibana at elasticsearch:
     $  /usr/share/elasticsearch/bin/elasticsearch-reset-password -i  -u kibana_system
     $  systemctl enable –now kibana 
     $   systemctl status kibana
     $  tail -n 200 /var/log/kibana/kibana.log


