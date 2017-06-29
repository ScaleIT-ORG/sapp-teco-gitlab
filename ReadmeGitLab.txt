1) docker-compose up -d
2) When you browse to specific IP you'll see the GitLab login page
3) You registrate there
4) While creating new project you may want to import a project from GitLab.com , in this case follow steps which are suggested on the website
   a)you can skip steps 8-9 and go to 10 step directly. Configurations from the step 10 you need 
     to add into docker-compose.yml after "GITLAB_OMNIBUS_CONFIG:"
	 Instead of your-gitlab.example.com write your IP.
      # for making imports from gitlab possible
      # gitlab_rails['omniauth_providers'] = [{    
      #"name" => "gitlab", 
      #"app_id" => "...",          #Application Id
      #"app_secret" => "...",      #Secret
      #"args" => { "scope" => "api" }}]
	 