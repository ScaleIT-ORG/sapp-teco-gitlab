##GitLab 
To run the GitLab Docker image, which is a monolithic image of GitLab running all the necessary services on a single container,using docker-compose.

###Requirements:
full description here https://docs.gitlab.com/ce/install/requirements.html
Memory: - size of a GitLab image: 1.16GB
        - 4GB RAM is the recommended memory size for all installations and supports up to 100 users
        - at least 2GB of swap, even if there is currently enough available RAM. Having swap will help reduce the chance of errors occurring if available 
memory changes.
        - size of the repos
CPU:    - 2 cores is the recommended number of cores and supports up to 500 users
Ports: 80
       11
       443

## Installing
1) docker-compose up -d
2) When you browse to specific IP you'll see the GitLab login page
3) You registrate there
4) Create projects and use great GitLab features




	 