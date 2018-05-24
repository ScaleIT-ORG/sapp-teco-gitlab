
  <img title="logo" src='https://raw.githubusercontent.com/ScaleIT-Org/sapp-teco-gitlab/master/Resources/logo-square.png' width="20%"/>


# Gitlab Image [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This is a monolithic image of GitLab running all the necessary services in a single container, using docker-compose.
[GitLab is an open source end-to-end software development platform with built-in version control, issue tracking, code review, CI/CD, and more.](https://about.gitlab.com)

## Architecture:


  <img title="architecture" width="50%" src='https://gitlab.msu.edu/help/development/gitlab_architecture_diagram.png' />


* MySQL or PostgreSQL are for persistent database information (e.g. users, permissions, issues, other metadata)
* Sidekiq is used as a job queue
* Redis is used as a non-persistent database backend for job information, metadata, and incoming jobs
* Nginx is used as a web front end to proxypass the Unicorn web server. 
* [more description about architecture is here](https://docs.gitlab.com/ce/development/architecture.html)

## Requirements:

### Memory: 
1. size of a GitLab image: 1.16GB
2. 4GB RAM is the recommended memory size for all installations and supports up to 100 users
3. at least 2GB of swap, even if there is currently enough available RAM. Having swap will help reduce the chance of errors occurring if available 
memory changes.
4. size of the repos

### CPU: 
1. 2 cores is the recommended number of cores and supports up to 500 users

### Ports:
1. 80 
2. 11
3. 443

* [full description here](https://docs.gitlab.com/ce/install/requirements.html)

## Installation
1. `docker-compose up -d `
2. Type in browser `gitlab.example.com` and you'll see the GitLab login page
3. You register there (admin account has a username "root" and a password that you typed at the beginning)
4. Create projects and use great GitLab features


### Problems that might happen

1. If while connecting to `gitlab.example.com` you see in browser first this :  

 
  <img title="502_1" width="50%" src='https://raw.githubusercontent.com/ScaleIT-Org/sapp-teco-gitlab/master/Resources/Screenshots/502_1.png' />
 
 Then in some time this :
 
 
  <img title="502_2"  width="50%" src='https://raw.githubusercontent.com/ScaleIT-Org/sapp-teco-gitlab/master/Resources/Screenshots/502_2.png' />
 
 
Then you should wait approximately 5 minutes.

2. While pushing some changes to GitLab, you become `413 (Request Entity Too Large)` error. It means that the size of files that you push exceeds the configured value.
In this case set label `"rap.client_max_body_size"` to a bigger value (e.g rap.client_max_body_size: 300m)or to 0. 0 disables checking of of client request body size.  <!-- (0 not tested yet) -->


## Docker-Compose

docker-compose.yml was created on a base of an [example from official Gitlab documentation](https://docs.gitlab.com/omnibus/docker/#install-gitlab-using-docker-compose)

## Adding Environment Variables

Due to the fact that docker-compose seems not to support more than 1 env variable if you use multi-line variables, you can use the "env_file" parameter.

```yml
services:
  gitlab:
    image: gitlab/gitlab-ce:10.5.2-ce.0
    env_file:
      - ./.env 
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://gitlab.example.com'	
```
.env.default file:
```
# env sourced from .env file
LDAPNAME=ldap.mydomain.com
PASSWORD=12345678
```

## Backups
It is recommended to backup `/etc/gitlab` (all configuration for omnibus-gitlab is stored here), or at least of `/etc/gitlab/gitlab-secrets.json`. 
To backup `/etc/gitlab` directory just run following command :
```
#Create a time-stamped .tar file
docker exec -t <your container name> /bin/sh -c 'umask 0077; tar cfz /secret/gitlab/backups/$(date "+etc-gitlab-\%s.tgz") -C / etc/gitlab'
```
To backup application :
```
docker exec -t <your container name> gitlab-rake gitlab:backup:create
```
* [Backups Documentation](https://docs.gitlab.com/omnibus/settings/backups.html)

## LDAP 

* Configurations in docker-compose.yml file in `"GITLAB_OMNIBUS_CONFIG: |"` part to make connection with LDAP server:

```yml
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = YAML.load <<-EOS
  main:
    label: 'LDAP'                # name for your LDAP server that will be shown on the web page
    host: '_your_ldap_server'    # Example: 'ldap.mydomain.com'
    port: '389'                  # 636 for SSL
    uid: 'sAMAccountName'        
    bind_dn: '_the_full_dn_of_the_user_you_will_bind_with' 
    password: '_the_password_of_the_bind_user'
    active_directory: 'false'    # This setting specifies if LDAP server is Active Directory LDAP server
    base: ''                     # Base where we can search for users
    method:'plain'               # Encryption method. "start_tls" which is tls or "simple_tls" which is ssl or "plain"
  EOS
```


* In our docker-compose.yml were used following configurations to test connection between GitLab and 
[Remote LDAP server](http://www.zflexsoftware.com/index.php/pages/free-online-ldap):


```yml
 gitlab_rails['ldap_enabled'] = true
 gitlab_rails['ldap_servers'] = YAML.load <<-EOS
   main:
     label: 'LDAP'
     host: 'www.zflexldap.com'
     port: '389'
     uid: 'uid'
     bind_dn: 'cn=ro_admin,ou=sysadmins,dc=zflexsoftware,dc=com' 
     password: 'zflexpass' 
     encryption: 'plain'
     active_directory: 'false'
     base: 'dc=zflexsoftware,dc=com'
  EOS
```
* [GitLab Documentation about LDAP](https://docs.gitlab.com/ce/administration/auth/ldap.html)

### LDAP Problems

 * to check if ldap works fine : `docker exec -it <your container name> gitlab-rake gitlab:ldap:check`
 
 * notice, that the field "uid" can be set to "sAMAccountName" (Windows AD) or "uid" (OpenLDAP). 
 E.g. in our case we use OpenLDAP, so "uid: 'uid'" otherwise GitLab will not see LDAP users. 
 
 * notice that by default LDAP service listens for connections on TCP and UDP port 389. LDAPS (LDAP over SSL) listens on port 636.
 

## External PostgreSQL server
By default, GitLab uses PostgreSQL server that is already included in Omnibus GitLab. You can also reconfigure it to use an external instance of PostgreSQL.

* Configurations in docker-compose.yml file in `"GITLAB_OMNIBUS_CONFIG: |"` :
```
 # Disable the built-in Postgres
 postgresql['enable'] = false 
 
 # connection details for database.yml
 gitlab_rails['db_username'] = "gitlab"
 gitlab_rails['db_password'] = "gitlab"
 gitlab_rails['db_host'] = "postgresql"
 gitlab_rails['db_port'] = "5432"
 gitlab_rails['db_database'] = "gitlabhq_production"
 gitlab_rails['db_adapter'] = 'postgresql'
 gitlab_rails['db_encoding'] = 'utf8'
```
* Create PostgreSQL service :
```
postgresql:
    restart: always
    image: postgres:9.6.2-alpine   #use PostgreSQL 9.6 version
    environment:
    - POSTGRES_USER=gitlab
    - POSTGRES_PASSWORD=gitlab
    - POSTGRES_DB=gitlabhq_production
    volumes:
    - postgresql:/var/lib/postgresql:rw
```

* [Documentation](https://docs.gitlab.com/omnibus/settings/database.html#using-a-non-packaged-postgresql-database-management-server)

## CI pipeline
Create Gitlab Runner as a sidecar :
```
gitlab-runner-sidecar:
    image: gitlab/gitlab-runner:latest
    restart: always
    volumes:
      - gitlab-runner-config:/etc/gitlab-runner \
      - /var/run/docker.sock:/var/run/docker.sock \
```
* [Create runner for docker-in-docker projects](https://docs.gitlab.com/ce/ci/docker/using_docker_build.html)

### Possible errors
 
 * ERROR: starting container process caused "exec: \"./nameofscript.sh\": permission denied"
 * SOLUTION: use ENTRYPOINT ["/bin/sh", "./nameofscript.sh"] if you want to execute script in DOCKERFILE 


	 
