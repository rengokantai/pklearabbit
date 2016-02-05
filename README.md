#### pklearabbit
 - cp3
 
 config user
 ```
 rabbitmq-server
 rabbitmqctl add_user user pass
 rabbitmqctl set_user_tags user administrator
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
