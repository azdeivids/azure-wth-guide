# Azure static web apps


1. Clone the GitHub repo to your local machine: [Github Repo](https://github.com/azdeivids/azure-swa.git).
2.  Sign into [Azure Portal](https://portal.azure.com) and create a [static web app](https://portal.azure.com/#create/Microsoft.StaticApp).
	- Select a subscription.
	- Select or create a new resource group.
	- Select a name for the app.
	- Select GitHub as the deployment type.
	- Select the free app plan.
	- Select the location closes to your users.
	- Authenticate against GitHub.
		1. Select the organization.
		2. Select the [repository containing the app](https://github.com/azdeivids/azure-swa.git).
		3. Select the branch.
		4. Select a custom build preset.
		5. In the App location enter /www for the frontend.
		6. In the Api location enter /api for the backend.
		7. In the Output location enter /www since the frontend does not require any build systems to run.
	- Review and create the app.
	- Once the app has been created pull down the repository as azure will have created aditional files within the repo during the deployment.

The added YAML file, that Azure created defines that the GitHUb Action is going to be trigered everytime there is a push on the main branch or every time a Pull Request is opened, synchronized, reopened or closed.

3. In GitHub, go to the static web app repository and click on *Actions* tab to view all workflows. 
![[Pasted image 20221226175138.png]]
4. 







# Resources
- [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
- [GitHub Actions Workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)
- [Azure Static Web Apps documentation](https://learn.microsoft.com/en-us/azure/static-web-apps/?WT.mc_id=javascript-76678-cxa)
- 