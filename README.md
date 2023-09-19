# Context
- it can be costly and complicated to setup aws environment for quick cloud infra pocs/development
- localstack provides a way to quicky mock cloud infra and has integration to other modern devops tools
- this gist is a quick and dirty runsheet to setup localstack

# Caution
- Localstack has free and paid version, have a study about what's on offer and dont expect everything for free
- When/if running in your own enterprise environment, your aws might have privilage boundaries applied, again some service might not work but this might not be due to localstack 

# Prerequisite
- wintel + wsl2 enabled + ubuntu 22
- docker desktop
- python 3.8+ (in ubuntu)
- aws cli
- terraform
- aws sam cli (if working with aws lambda via sam)
- locastack 2.2.0

# Steps
- get wsl2 ubuntu22 and docker desktop installed and working
- within your python venv or conda env, install localstack
  - `pipip install localstack==2.2.0`
- once installed create a directory somewhere to store your docker-compose
  - ref: https://docs.localstack.cloud/getting-started/installation/#docker-compose
  - `mkdir ~/localstack && cd localstack`
  - copy content of https://gist.github.com/tenshi13/0d46aee3799749bd52bd90beeadaa750 to `~/localstack/docker-compose.yml`
  - docker-compose up (will take some time to load)
  - try to access the web admin console via https://app.localstack.cloud, (will need to login with your github account)
  - once logged in, go to `Localstack Instances` -> `Default` to see the infra created locally
- extra tools to make life easier (ref: https://docs.localstack.cloud/user-guide/integrations/)
  - awslocal
    - pip install awscli-local
    - a wrapper for aws cli to target the localstack infra on your local
  - tflocal
    - pip install terraform-local
    - a wrapper for terraform to target the localstack infra on your local
  - samlocal
    - pip install aws-sam-cli-local
    - a wrapper for aws sam cli to target the localstack infra on your local
  
# Quick Test
- we can test quickly by creating some local infra
- let's create a new s3 bucket
  - in your ubuntu : `awslocal s3 mb s3://ethantest1`
  - go to localstack web console's default instances -> s3
  - click the refresh we should be able to see the newly created s3 bucket, this means its working now

# Persistance
- atm localstack is ephimeral, the infra will get blown away everytime the containers stop
- we can make it so the state is save and loaded using localstack's docker event hooks
  ref: https://github.com/localstack/localstack/issues/6281
- after adjusting the docker-compose by adding `boot.sh`, `ready.sh` and `shutdown.sh`, we can redo the quick test
- Create the s3 bucket again, shut down local stack (just ctrl-c it), restarting it (docker-compose up again) - it should say `Restoring from pod`, and if we check local s3 our bucket should be present

# Ref
- https://docs.localstack.cloud/getting-started/installation/#docker-compose
- https://docs.localstack.cloud/user-guide/integrations/
- https://stackoverflow.com/questions/66678846/how-do-i-get-my-certificates-to-work-for-ssl-in-localstack
- https://github.com/localstack/localstack/issues/6281