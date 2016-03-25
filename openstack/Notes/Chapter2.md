# Chapter 2: Installation

## Controller node

  1. Rabbitmq
  2. Mysql
  3. Keystone
  4. Glance
  5. Neutron
  6. Nova expect nova-compute
  7. Cinder expect cinder-compute
  8. Horizon

## Keystone

  1. apt-get install -y keystone
  2. edit /etc/keystone/keystone.conf  to set admin_token, connections, debug and verbose
  3. mysql -uroot -p -e "CREATE DATABASE keystone;"
  4. mysql -uroot -p -e "GRANT ALL PRIVILEGES ON keystone.* TO 'keystoneuser'@'localhost' IDENTIFIED BY 'openstack';"
  5. keystone-manage db_sync to initialize the table
  6. service keystone restart
  7. Check keystone:
          1. service keystone status
          2. check log in /var/log/keystone/keystone.log
          3. run keystone command: 
                # export OS_SERVICE_TOKEN=<admin_token> ##set in keystone.conf
                # export OS_SERVICE_ENDPOINT=http://127.0.0.1:35357/v2.0/
                # keystone user-list


tenant 1:N roles
tenant 1:N users
user   1:N roles

### Commands in Keystone
    keystone tenant-create --name=admin
    keystone user-create --name=admin --pass="openstack"
    keystone role-create --name=admin
    keystone user-role-add --user admin --role admin --tenant admin
    keystone service-create --name keystone --type identity --description 'Openstack Identity'
    keystone endpoint-create --service-id <service-id> --publicurl 'http://10.0.2.15:5000/v2.0' --adminurl 'http://10.80.80.10:35357/v2.0' --internalurl 'http://10.80.80.10:5000/v2.0'


## Glance

  1. apt-get install -y glance
  2. edit config file in /etc/glance/glance-api.conf and /etc/glance/glance-registry.conf
  3. mysql -uroot -p -e "CREATE DATABSE glance;"
  4. mysql -uroot -p -e "GRANT ALL PRIVILEGET ON glance.* TO 'glanceuser'@'localhost' IDENTIFIED BY 'openstack';"
  5. keystone tenant-create --name=service
  6. keystone user-create --name=glance --pass=openstack
  7. keystone user-role-add --user glance --role admin --tenant service
  8. keystone service-create --name glance --type image --description 'Openstack Image Service'
  9. keystone endpoint-create --service-id <service-id> --publicurl 'http://10.0.2.15:9292' --adminurl 'http://10.80.80.10:9292/' --internalurl 'http://10.80.80.10:9292/'
  10. glance-manage db_sync 
  11. service glance-api restart
  12. service glance-registry restart
  13. check glance:
        # service glance-api status
        # service glance-registry status
        # glance image-list

### Commands in Glance
    # Get the image
    wget http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-disk.img
    
    glance image-create --name myFirstImage --is-public true --container-format bare --disk-format qcow2 --file cirros-0.3.1-x86_64-disk.img
    glance image-list

  
## Neutron

1. apt-get install -y neutron-server
2. edit /etc/neutron/neutron.conf and /etc/neutron/ovs_neutron_plugin.ini
3. mysql -uroot -p -e "CREATE DATABASE neutron;"
4. mysql -uroot -p -e "GRANT ALL PRIVILEGES ON neutron.* TO 'neutronuser'@'%' IDENTIFIED BY 'openstack';"
5. myql -uroot -p -e "GRANT ALL PRIVILEGES ON neutron.* TO 'neutronuser'@'localhost' IDENTIFIED BY 'openstack';"
6. keystone user-create --name=neutron --pass=password
7. keystone user-role-add --user neutron --role admin --tenant service
8. keystone service-create --name neutron --type network --description 'Openstack Networking service'
9. keystone endpoint-create --service-id <service-id> --publicurl 'http://10.0.2.15:9696/' --adminurl 'http://10.80.80.10:9696/' --internalurl 'http://10.80.90.10:9696/'

## Nova

1. apt-get install -y nova-api nova-cert novnc nova-consoleauth nova-scheduler nova-novncproxy nova-doc nova-conductor
2. edit /etc/nova/api-paste.ini and /etc/nova/nova.conf
3. mysql -uroot -p -e "CREATE DATABASE nova;"
4. mysql -uroot -p -e "CRANT ALL PRIVILEDGES ON nova.* TO 'novauser'@'%' IDENTIFIED BY 'openstack';"
5. mysql -uroot -p -e "GRANT ALL PRIVILEDGES ON nova.* TO 'novauser'@'localhost' IDENTIFIED BY 'openstack';"
6. nova-manage db sync
7. cd /etc/init.d/; for i in $(ls nova-*); do sudo service $i restart; done
8. check the services with: nova-manage service list
9. keystone user-create --name=nova --pass=openstack
10. keystone user-role-add --user nova --role admin --tenant service
11. keystone service-create --name nova --type compute --description 'Openstack Computing Sevice'
12. keystone endpoint-create --serive-id <service-id> --publicurl 'http://10.0.2.15:8774/v2/$(tenant_id)s' --adminurl 'http://10.80.80.10:8774/v2/$(tenant_id)s' --internalurl 'http://10.80.80.10:8774/v2/$(tenant_id)s'
13. nova image-list

## Cinder
cinder-api cinder-scheduler cinder-volume

1. apt-get install -y cinder-api cinder-scheduler
2. edit /etc/cinder/api-paste.ini and /etc/cinder/cinder.conf
3. mysql -uroot -p -e "CREATE DATABASE cinder;"
4. mysql -uroot -p -e "GRANT ALL PRIVILEDGES ON cinder.* TO 'cinderuser'@'localhost' IDENTIFIED BY 'openstack';"
5. mysql -uroot -p -e "GRANT ALL PRIVILEDGES ON cinder.* TO 'cinderuser'@'localhost' IDENTIFIED BY 'openstack';"
6. cinder-manage db sync
7. keystone user-create --name=cinder --pass=openstack
8. keystone user-role-add --user cinder --role admin --tenant service
9. keystone service-create --name=cinder --type=volume --description="Cinder Volume Service"
10. keystone endpoint-create --service-id <service-id> --publicurl 'http://10.0.2.15:8776/v1/%\(tenant_id\)s' --internalurl 'http://10.80.80.10:8776/v1/%\(tenant_id\)s' --adminurl 'http://10.80.80.10:8776/v1/%\(tenant_id\)s'
11. service cinder-api restart
12. service cinder-scheduler restart
13. cinder-manage service list
