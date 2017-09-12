<p align="center">
  <img title="502" heigh="600" width="600" src='https://raw.githubusercontent.com/kristkat/custom-templates-portainer/master/stacked_wm_no_bg.png' />
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

* Configurations that should be added to docker-compose.yml file to "GITLAB_OMNIBUS_CONFIG: |" part:
```yml

```
* Here was taken remote [LDAP server]()



	 