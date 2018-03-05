<p align="center">
  <img title="502" heigh="200" width="200" src='https://raw.githubusercontent.com/kristkat/pictures/master/logo-square.png' />
</p>

This is a monolithic image of GitLab running all the necessary services in a single container, using docker-compose.
![GitLab is an open source end-to-end software development platform with built-in version control, issue tracking, code review, CI/CD, and more.](https://about.gitlab.com)

## Architecture:

<p align="center">
  <img title="502" heigh="600" width="600" src='https://gitlab.msu.edu/help/development/gitlab_architecture_diagram.png' />
</p>

* MySQL or PostgreSQL are for persistent database information (e.g. users, permissions, issues, other meta data)
* Sidekiq is used as a job queue
* Redis is used as a non-persistent database backend for job information, meta data, and incoming jobs
* Nginx is used as a web front end to proxypass the Unicorn web server. 
* [more description about architecture is here](https://docs.gitlab.com/ce/development/architecture.html)

## Requirements:
* [full description here](https://docs.gitlab.com/ce/install/requirements.html)

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

## Installing
1. docker-compose up -d 
2. Type in browser teco-gitlab.staging.teco.edu and you'll see the GitLab login page
3. You registrate there (admin account has an username "root" and a password that you typed at the beginning)
4. Create projects and use great GitLab features

## Adding Environment Variables

Due to the fact that docker-compose seems not to support more than 1 env variable if you use multi line variables, you can use the "env_file" paramter.

```yml
services:
  gitlab:
    image: gitlab/gitlab-ce:10.5.2-ce.0
    env_file:
      - ./.env 
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://testing.teco.edu'
```

## Problems that might happen

1. If while connecting to teco-gitlab.staging.teco.edu you see in browser first this :  
 <p align="left">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/pictures/master/502_1.png' />
</p> 
 Then in some time this :
<p align="left">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/pictures/master/502_2.png' />
</p>
 Then you schould wait approximately 5 minutes.
 
2. While pushing some changes to GitLab, you become 413 (Request Entity Too Large) error. It means that the size of files that you push exceeds the configured value.
In this case set label "rap.client_max_body_size" to a bigger value (e.g rap.client_max_body_size: 300m)or to 0. 0 disables checking of of client request body size.(0 not tested yet)

3. Might happen that 80 port that gitlab use is not free. To check what uses command : netstat -pna | grep 80  .
 To kill all processes that listen on this port : fuser -k 80/tcp  .(not relevant anymore because of nginx but useful commands)


## Backups

* [Documentation](https://docs.gitlab.com/omnibus/settings/backups.html)

## LDAP 

* [GitLab Documentation about LDAP](https://docs.gitlab.com/ce/administration/auth/ldap.html)

* Configurations in docker-compose.yml file in "GITLAB_OMNIBUS_CONFIG: |" part to make connection with LDAP server:

<p align="left">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/pictures/master/yml1.png' />
</p>

* In our docker-compose.yml were used following configurations to test connection between GitLab and 
[Remote LDAP server](http://www.zflexsoftware.com/index.php/pages/free-online-ldap):

<p align="left">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/pictures/master/yml2.png' />
</p>

### LDAP Problems

 * to check if ldap works fine : docker exec -it gitlab_container_id_or_name gitlab-rake gitlab:ldap:check
 
 * notice, that the field "uid" can be set to "sAMAccountName" (Windows AD) or "uid" (openldap). 
 E.g. in our case we use openldap, so "uid: 'uid'" otherwise GitLab will not see LDAP users. 
 
 * notice that by default a LDAP service listens for connections on TCP and UDP port 389. LDAPS (LDAP over SSL) listens on port 636.
 

## External PostgreSQL server

* [Documentation](https://docs.gitlab.com/omnibus/settings/database.html#using-a-non-packaged-postgresql-database-management-server)

## CI pipeline

* [Create runner for docker-in-docker projects](https://docs.gitlab.com/ce/ci/docker/using_docker_build.html)

### Possible errors
 
 * ERROR: starting container process caused "exec: \"./nameofscript.sh\": permission denied"
 * SOLUTION: use ENTRYPOINT ["/bin/sh", "./nameofscript.sh"] if you want to execute script in DOCKERFILE 


	 
