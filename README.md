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
