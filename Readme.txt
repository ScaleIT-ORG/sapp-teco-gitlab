How to start:
1) docker-compose up -d
2) When you browse to specific IP you'll see the GitLab login page
3) You registrate there
4) Create projects and use great GitLab features

Import from GitLab.com:
In case you may want to import a project from GitLab.com  while creating new project,follow steps which are suggested on the website
   a)you can skip steps 8-9 and go to 10 step directly. Configurations from the step 10 you need 
     to add into docker-compose.yml after "GITLAB_OMNIBUS_CONFIG:"
	 Instead of your-gitlab.example.com write your IP.
      # for making imports from gitlab possible
      # gitlab_rails['omniauth_providers'] = [{    
      #"name" => "gitlab", 
      #"app_id" => "...",          #Application Id
      #"app_secret" => "...",      #Secret
      #"args" => { "scope" => "api" }}]

Requirements:
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


	 