Ansible Role: HAProxy
=========
Installs HAProxy on RedHat/CentOS and Debian/Ubuntu Linux servers.

**Note**: This role _officially_ supports HAProxy versions 1.4 or 1.5. Future versions may require some rework.

Requirements
------------
* Ansible 2.2+ installed on the control machine.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    haproxy_socket: /var/lib/haproxy/stats

The socket through which HAProxy can communicate (for admin purposes or statistics). To disable/remove this directive, set `haproxy_socket: ''` (an empty string).

    haproxy_chroot: /var/lib/haproxy

The jail directory where chroot() will be performed before dropping privileges. To disable/remove this directive, set `haproxy_chroot: ''` (an empty string). Only change this if you know what you're doing!

    haproxy_user: haproxy
    haproxy_group: haproxy

The user and group under which HAProxy should run. Only change this if you know what you're doing!

    haproxy_frontend_name: 'hafrontend'
    haproxy_frontend_bind_address: '*'
    haproxy_frontend_port: 80
    haproxy_frontend_mode: 'http'

HAProxy frontend configuration directives.

    frontends:
        - name: front1
          haproxy_frontend_bind_address: "192.168.0.1"
          haproxy_frontend_port: 8080
          haproxy_frontend_mode: 'tcp'
          backend_name: "backend-1"
        - name: front2
          haproxy_frontend_bind_address: "192.168.0.1"
          haproxy_frontend_port: 80
          haproxy_frontend_mode: 'http'
          backend_name: "backend-2"


HAProxy backend configuration directives.

    backends:      
      - name: backend-1
        haproxy_backend_name: 'backend-1'
        haproxy_backend_mode: 'tcp'
        haproxy_backend_balance_method: 'roundrobin'
        haproxy_backend_servers:
          - name: prod-k8s-1
            address: 192.168.10.1:8080
      - name: backend-2
        haproxy_backend_name: 'backend-2'
        haproxy_backend_mode: 'http'
        haproxy_backend_balance_method: 'roundrobin'
        haproxy_backend_servers:
          - name: ingress-k8s-1
            address: 192.168.10.2:80

A list of backend servers (name and address) to which HAProxy will distribute requests.

    haproxy_connect_timeout: 5000
    haproxy_client_timeout: 50000
    haproxy_server_timeout: 50000

HAProxy default timeout configurations.

    haproxy_global_vars:
      - 'ssl-default-bind-ciphers ABCD+KLMJ:...'
      - 'ssl-default-bind-options no-sslv3'

A list of extra global variables to add to the global configuration section inside `haproxy.cfg`.

Keepaleived variables

    haproxy_keepalived_enable: true
    haproxy_keepalived_interface: eth0
    haproxy_keepalived_state: BACKUP
    haproxy_keepalived_priority: 100
    haproxy_keepalived_virtual_router_id: 70
    haproxy_keepalived_advert_int: 2
    haproxy_keepalived_auth_pass: secretheare
    haproxy_keepalived_vip: ""
    haproxy_vip_interface: eth0 

![Demo](assets/haproxy-with-keepalived.gif)


Dependencies
--------------

None.

Example Playbook
--------------

    - hosts: balancer
      sudo: yes
      roles:
        - { role: karen.haproxy }

License
--------------

This role is licensed under the MIT License.

Author Information
--------------

This role was created in 2023 by Abbos Pulatov
