#### pklearabbit
install rabbitmq
```
echo 'deb http://www.rabbitmq.com/debian/ testing main' |sudo tee /etc/apt/sources.list.d/rabbitmq.list && wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc |sudo apt-key add - && sudo apt-get update && sudo apt-get install -y rabbitmq-server
```
#####cp3
###### Adminstering Rabbitmq instances
config user (we create user=root pass=root vhost=test)
```
service rabbitmq-server status
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl add_user user pass
rabbitmqctl set_user_tags user administrator
rabbitmqctl change_password old new
rabbitmqctl delete_user user
```
 
config vhost
```
rabbitctl add_vhost test
rabbitctl list_vhosts
rabbitctl delete_vhost test
```

set config, write, read permission of a vhost. 
```
rabbitmqctl set_permissions -p test user ".*" ".*" ".*"
rabbitmqctl clear_permissions -p test user
rabbitmqctl clear_permissions -p user  //clear all vhosts permission
```
using python
declare a permission(first, change permission)
```
cd /var/lib/rabbitmq/mnesia/rabbit@a-plugins-expand/rabbitmq_management-3.6.2/priv/www/cli
chmod +x rabbitmqadmin
```
then
```
root@a:/var/lib/rabbitmq/mnesia/rabbit@a-plugins-expand/rabbitmq_management-3.6.2/priv/www/cli# ./rabbitmqadmin declare permission vhost=test user=root configure=.* write=.* read=.*
```
delete a permission
```
root@a:/var/lib/rabbitmq/mnesia/rabbit@a-plugins-expand/rabbitmq_management-3.6.2/priv/www/cli# ./rabbitmqadmin delete permission user=root vhost=test
```
list a user's vhosts
```
./rabbitmqadmin -u root -p root list vhosts
```


declare exchange (python or GUI) (must with -u and -p)
```
./rabbitmqadmin declare exchange name=logs type=fanout -u root -p root
./rabbitmqadmin declare -V test exchange name=logs type=fanout -u root -p root  // in volume
```
list all exchanges
```
./rabbitmqadmin -V test list exchanges -u root -p root
```

Administering queue(python)
```
./rabbitmqadmin declare queue name=error_logs durable=false  //without auth
./rabbitmqadmin -V test declare queue name=error_logs -u root -p root   //create a queue for volume, needs auth
./rabbitmqadmin -V test delete queue name=error_logs -u root -p root
./rabbitmqadmin list queues  //default
./rabbitmqadmin -V test list queues -u root -p root  //for volume name
```

Administering binding(python)
bind:
```
./rabbitmqadmin declare binding source=logs destination=error_logs    //bind logs exchange to error_logs queue
```
post a message to queue
```
./rabbitmqadmin publish exchange=logs routing_key= payload="test"
```
get message from error_logs queue:
```
./rabbitmqadmin get queue=error_logs
```
purge queue
```
./rabbitmqadmin purge queue name=error_logs   //mind this very weird syntax
```

Administering policies
```
rabbitmqctl set_policy max-queue-len "error_logs" '{"max-length-bytes":20000}' --apply-to queues //nix style
rabbitmqctl set_policy max-queue-len "error_logs" "{""max-length-bytes"":20000}" --apply-to queues //windows style
```
clear
```
rabbitmqctl clear_policy max-queue-len
```
another test, set message ttl policy
```
rabbitmqctl set_policy ttl ".*" '{"message-ttl":3000}' --apply-to queues   //message ttl
```
queue ttl
```
rabbitmqctl set_policy ttl ".*" '{"expires":3000}' --apply-to queues   //message ttl
```

clear
```
rabbitmqctl clear_policy ttl
```

test queue consuming
```
./rabbitmqadmin declare policy name=ttl pattern="^error_logs$" definition='{"dead-letter-exchange":"logs_dlx","message-ttl":3000}' apply-to=queues
ueues
./rabbitmqadmin declare queue name=error_logs_dlx
./rabbitmqadmin declare binding source=logs_dlx destination=error_logs_dlx
./rabbitmqadmin publish exchange=logs routing_key= payload="xxx"
./rabbitmqadmin get queue=error_logs_dlx
./rabbitmqadmin purge queue=error_logs_dlx
```

python style
```
./rabbitmq list policies
./rabbitmq delete policy name=ttl
```
Backing up broker metadata
```
./rabbitmqadmin export broker.json
./rabbitmqadmin export broker.json
```
######Installing RabbitMQ plugins
```
rabbitmq-plugins enable rabbitmq_federation
```
#####cp4 clustering
first, disable all plugins
```
rabbitmq-plugins disable rabbitmq_management
```

Create cluster.(blood) hostname must be uppcase+rabbit, E.x. RABBIT1, RABBIT2
machine1:
```
vim `find / -name .erlang.cookie`  //copy key
```
machine2/3:  
Edit host:
```
<private ip> RABBIT1 RABBIT1
```
Edit cookie:
```
(same as machine1)
```
start:
```
rabbmimqctl stop_app
rabbitmqctl join_cluster rabbit@RABBIT1
rabbitmqctl start_app
```
reactive plugins:  
node1:
```
rabbitmq-plugins enable rabbitmq_management
```
others
```
rabbitmq-plugins enable rabbitmq_management_agent
```
check cluster status
```
rabbitmqctl cluster_status
```
REMOVE rabbit2 from rabbit1:
```
rabbitmqctl stop_app   //rabbit2
rabbitmqctl forget_cluster_node rabbit@RABBIT2  //rabbit1
rabbitmqctl reset  //rabbit2
rabbitmqctl start_app  //rabbit2
```

#####cp7 Performance Tuning
######Performance tuning of rabbitmq
memory watermark(original=0.4,if ram=512mb,watermark=196mb)
```
rabbitmqctl set_vm_memory_high_watermark 0.8
```
set config file, location should be
```
/etc/rabbitmq/rabbitmq-env.conf  //default not vailable, search by
find / -name rabbitmq.*
```


######Monitoring of rabbitmq instances
```
apt-get update
apt-get install nagios3 nagios-nrpe-plugin
```
login:
```
ip/nagios3
```
auth:
```
nagiosadmin:pass
```

#####chap9 security
######Authentication
```
apt-get install slapd ldap-utils
```

