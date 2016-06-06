#### pklearabbit
install rabbitmq
```
echo 'deb http://www.rabbitmq.com/debian/ testing main' |sudo tee /etc/apt/sources.list.d/rabbitmq.list
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc |sudo apt-key add -
sudo apt-get update && sudo apt-get install -y rabbitmq-server
```
#####cp3
###### Adminstering Rabbitmq instances
config user
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
rabbitctl add_vhost chat
rabbitctl list_vhosts
rabbitctl delete_vhost chat
```

set config, write, read permission of a vhost. 
```
rabbitmqctl set_permissions -p chat user ".*" ".*" ".*"
rabbitmqctl clear_permissions -p chat user
rabbitmqctl clear_permissions -p user  //clear all vhosts permission
```
using python
declare a permission
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



