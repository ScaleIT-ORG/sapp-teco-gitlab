<p align="center">
  <img title="502" heigh="200" width="200" src='https://raw.githubusercontent.com/kristkat/custom-templates-portainer/master/stacked_wm_no_bg.png' />
</p>
# Creating GitLab image
To run the GitLab Docker image, which is a monolithic image of GitLab running all the necessary services on a single container,using docker-compose.

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
2. Type in browser gitlab.staging.teco.edu and you'll see the GitLab login page
3. You registrate there
4. Create projects and use great GitLab features

## Problems that might happen

* If while connecting to gitlab.staging.teco.edu you see in browser first this :  
 <p align="left">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/pictures/master/502_1.png' />
</p> 
* Then in some time this :
<p align="left">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/pictures/master/502_2.png' />
</p>
* Then you schould wait approximately 5 minutes.

## Backups

* [Documentation](https://docs.gitlab.com/omnibus/settings/backups.html)

## LDAP 

* [GitLab Documentation about LDAP](https://docs.gitlab.com/ce/administration/auth/ldap.html)

* Configurations in docker-compose.yml file in "GITLAB_OMNIBUS_CONFIG: |" part to make connection with LDAP server:

``` yml
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = YAML.load <<-EOS 
main:
label: 'LDAP' 
host: '_your_ldap_server'  # Example: 'ldap.mydomain.com'
port: 389 # usually 636 for SSL
uid: 'sAMAccountName' # This should be the attribute, not the value that maps to uid.
bind_dn: '_the_full_dn_of_the_user_you_will_bind_with'
password: '_the_password_of_the_bind_user'
active_directory: false   # This setting specifies if LDAP server is Active Directory LDAP server.
base: ''                  # Base where we can search for users
method: 'plain'           # Encryption method
EOS
```
* In our docker-compose.yml were used following configurations to test connection between GitLab and 
[Remote LDAP server](http://www.zflexsoftware.com/index.php/pages/free-online-ldap):

``` yml
gitlab_rails['ldap_enabled'] = true
        gitlab_rails['ldap_servers'] = YAML.load <<-EOS
         main:
          label: 'LDAP'
          host: 'www.zflexldap.com'
          port: 389
          uid: 'uid'     
          bind_dn: 'cn=ro_admin,ou=sysadmins,dc=zflexsoftware,dc=com'
          password: 'zflexpass'
          active_directory: false
          base: 'dc=zflexsoftware,dc=com'
          method: 'plain' 
          EOS
```




	 