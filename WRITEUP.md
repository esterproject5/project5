# Write-up Template

### Analyze, choose, and justify the appropriate resource option for deploying the app.

*For **both** a VM or App Service solution for the CMS app:*
- *Analyze costs, scalability, availability, and workflow*
- *Choose the appropriate solution (VM or App Service) for deploying the app*
- *Justify your choice*


- VMs are more expensive and time consuming. they allow you full access and control of the VM. They are an excellent choice for migrating from on-premises server to the cloud. Multiple VMs can be grouped to provide high availability, scalability and redundacy. 

- Azure App Services support multiple languages, it is high availability, auto-scaling, and supoort of both Linux and Windows environments. It is a continues deployment model using Github, azure devops, or any Git repo. It has vertical and horizontal scaling. It has three different tiers. We can use a free option within Dev/Test. 

- I am choosing the Azure App Services
- I am using the Azure App Services because I am building a lightweight application and services. I dont need high performance compute services. I will also do continous deployments and using Github and App services allows this. Once the application is up and running, App services allows me to scale vertically when there are different demands. 

### Assess app changes that would change your decision.

*Detail how the app and any other needs would have to change for you to change your decision in the last section.* 

To change from an app service to using VMS there would have to be different requirements. For example if the underlying OS needs to be a certain requirement, also if there will be a vast of users and articles in the future for dedicated servers for security reasons. If images are of better quality as well. Other requirements like a user profile that will have user information, or if there will be any type of analytics involved. 