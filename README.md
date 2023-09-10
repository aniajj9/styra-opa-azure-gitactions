This repo contains files for build of 2 communicating docker containers: STYRA CTF and OPA

### How to deploy it on Azure web app?

1. Create 2 web apps on azure - one for opa, other for styra
2. They need to be initialized with a single docker container - it is a good idea to have the two containers (built using Dockerfile_opa and Dockerfile_ctf) on Azure Container Registry, to see if the containers work. Then the two web apps can be initialized with them.
3. Create an application variable (Settings - Configuration - Application Settings) called "WEBSITES_PORT". Both apps should have value of the exposed port from dockerfile (default for Styra: 3001, for Opa: 8181)
4. Under Deployment, select from github repo. Link to this repo, and create workflow file automatically
5. On Github, under "Actions", you can monitor the deployment. !!! The first deployment should fail !!! - the automatic script looks for a file called "Dockerfile", which here doesn't exist. Change the "Dockerfile" to appropriate files ("Dockerfile_opa" for Opa, and "Dockerfile_ctf" for styra)
6. In "main.go" file find one reference to Opa's address (something like "http://opa:8181/v1/data/ctf/task%d/decision"). Change the address to the address of the Opa's domain, leaving ":8181/v1/data/ctf/task%d/decision" unchanged.
7. Wait until redeployment is complete. It might be necessary to restart applications on Azure, or wait a few minutes.