# Playbook

```
---
- name: Deploy LAMP environment with Ansible
 hosts: all
 become: yes
 tasks:
  - name: Update package cache and install required packages
   yum:
    name: "{{ item }}"
    state: present
   loop:
    - httpd
    - mariadb-server
    - php
    - php-mysql
    - MySQL-python
   notify: restart apache
 
  - name: Start and enable Apache service
   service:
    name: httpd
    state: started
    enabled: yes
 
  - name: Start and enable MySQL service
   service:
    name: mariadb
    state: started
    enabled: yes
 
  - name: Create MySQL database and user
   mysql_db:
    name: my_database
    state: present
   notify: restart mysql
 
  - name: Create MySQL user
   mysql_user:
    name: wh
    password: abc
    priv: 'my_database.*:ALL'
    host: localhost
 
  - name: Import initial SQL file
   mysql_db:
    name: my_database
    state: import
    target: /path/to/initial.sql
 
  - name: Configure virtual host
   template:
    src: /path/to/virtualhost.conf.j2
    dest: /etc/httpd/conf.d/my_website.conf
   notify: restart apache
 
 
  - name: p1
   copy:
     src: /var/www/html/index.php
     dest: /var/www/html/index.php
 
 handlers:
  - name: restart apache
   service:
    name: httpd
    state: restarted
 
  - name: restart mysql
   service:
    name: mariadb
    state: restarted
 
 
 
 
 
 
```

`关闭`` ``客户机防火墙```

```
sudo systemctl stop firewalld
sudo systemctl disable firewalld
sudo systemctl mask firewalld
 
 
Virtualhost.conf.j2
<VirtualHost *:80>
  ServerAdmin admin@example.com
  DocumentRoot /var/www/html
  ServerName example.com
  ErrorLog logs/example.com_error_log
  CustomLog logs/example.com_access_log common
</VirtualHost>
 
 
Index.php (php``网页``)
 
<?php
$servername = "localhost";
$username = "wh";
$password = "abc";
$dbname = "my_database";
 
```

`// ``创建连接```

```
$conn = new mysqli($servername, $username, $password, $dbname);
 
```

`// ``检查连接```

```
if ($conn->connect_error) {
  die("``连接失败``: " . $conn->connect_error);
}
 
```

`// ``执行查询```

```
$sql = "SELECT * FROM your_table";
$result = $conn->query($sql);
 
if ($result->num_rows > 0) {
```

`  // ``输出数据```

```
  while($row = $result->fetch_assoc()) {
    echo "id: " . $row["id"]. " - Name: " . $row["name"]. "<br>";
  }
} else {
  echo "0 ``结果``";
}
 
$conn->close();
?>
```

#### 操作具体步骤与遇到问题

1.创建一个名为wh3.yaml的Ansible Playbook文件。安装epel-release ansible linux-python

2.Ssh 连接虚拟机,cd /etc/ansible vi hosts

3.[all]

192.......

192.......

4.在Playbook中定义主机组和变量，确保可以在多台服务器上并行执行。

5.使用yum模块安装所需的软件包，如Apache/Nginx、MySQL和PHP。

遇到问题：安装包版本兼容centos 中apeche 是httpd

6使用service模块启动并设置这些服务。

7使用copy模块复制网站文件到服务器上，也可以使用template模块根据需要生成配置文件。

遇到问题：主机与目标用户目录未创建与复制

\8.   使用mysql_db和mysql_user模块管理MySQL数据库，创建数据库和用户。

遇到问题：用户创建不匹配

\9.   使用mysql_db模块导入初始的SQL文件到数据库中。

遇到问题：数据库导入安装包下python

10.编写一个index.php 配置一下网页检测一下是否连接成功mysql数据库，template模块和copy模块，客户机上配置成功

11.配置虚拟主机，可以使用template模块生成虚拟主机配置文件，并使用service模块重新加载Apache/Nginx配置。

12.最后，编写一个任务来检查网站是否可以正常访问