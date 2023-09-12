# Deploying Two Docker Containers (Styra CTF and OPA) to Azure Web Apps:

## 1. Create Azure Web Apps
- Create two separate Azure Web Apps: one for the Styra CTF and another for OPA. You can do this through the Azure portal, or CLI.

## 2. Initialize Web Apps
- You can initialize the two Web Apps with any container image at this point. However, it's recommended to use the containers that correspond to the desired application to simplify debugging later on. 
  - To deploy Styra CTF and OPA, it is recommended to build containers locally and push them to Azure Container Registry. It might be necessary to uze Azure File Storage for any files that need to be mounted.
  - The benefit of using Azure Container Registry, is that it needs to be setup regardless of the chosen solution, as it will be later used to store the containers built using Github Actions. If the app has been initialized using an image from AC, then the login informations are automatically transferred to the workflow file and Github secrets. Otherwise, it would have been needed to set them up manually.

## 3. Configure Application Settings
- For each Azure Web App, go to "Settings" > "Configuration" > "Application Settings."
- Add an application setting called "WEBSITES_PORT" with the value set to the exposed port from the respective Dockerfile. As a default, use "3001" for Styra CTF and "8181" for OPA.

## 4. Set up Deployment from GitHub
- In the Azure portal, navigate to your Web Apps.
- Under "Deployment," select the option to deploy from a GitHub repository.
- Link the GitHub repository containing the Dockerfiles and application code. This will automatically create a workflow file in the repository (usually under `.github/workflows`) to automate deployments.

## 5. Modify GitHub Workflow
- !!! **The first automatic deployment should fail** !!! 
  - The generated GitHub Actions workflow will likely look for a default "Dockerfile," which doesn't exist in this case.
- Update the workflow files (`Dockerfile` references) to use the appropriate Dockerfiles for each application. Use "Dockerfile_opa" in the workflow file for OPA and "Dockerfile_ctf" in the workflow file for Styra CTF.

## 6. Update Application References
- In the "main.go" file, locate the reference to OPA's address. Replace the address with the domain or endpoint of your OPA container, but keep the path portion ("/v1/data/ctf/task%d/decision") unchanged.

## 7. Monitor Deployment
- In the GitHub repository, go to the "Actions" tab to monitor the deployment process. Initially, the first deployment should fail due to the Dockerfile references, but subsequent deployments should work correctly.

## 8. Finalize Deployment
- After the deployment is successful, you may need to restart the Azure Web Apps or wait a few minutes for them to fully initialize and start serving your applications. 

## 9. Continuous deployment
- The subsequent changes to any file in "main" branch should trigger redeployment of both Styra CTF and OPA applications.

## 10. Setting up virtual network
- Since only the Styra app needs to be publicly available, a virtual network can be set up to deny access to OPA.
  1. Create a Virtual Network in the same resource group as the two applications
  2. Create a subnet in the Virtual Network
  3. In "Settings" > "Networking" > "Outbound Traffic" enable VNet integration
  4. Add a VNet configuration, choosing the created subnet. Make sure "Route All" setting is enabled
  5. For OPA only: allow "Access Restrictions" in "Inbound Traffic"
  6. In "Access Restrictions" make sure "Allow public access" is enabled
  7. Add an "" 

