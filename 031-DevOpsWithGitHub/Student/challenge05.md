# What The Hack: DevOps with GitHub 

## Challenge 5 - Infrastructure as Code (IaC)

[< Previous](challenge04.md) - [Home](../readme.md) - [Next >](challenge06.md)

### Introduction

Now that we have some code, we need an environment to deploy it to! The term Infrastructure as Code (IaC) refers to using templates (code) to repeatedly and consistently create the dev, test, prod (infrastructure) environments. We can automate the process of deploying the Azure services we need with an Azure Resource Manager (ARM) template. invoked from automation in a GitHub Actions workflow 

Review the following articles:

- [Azure Resource Manager overview](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)
- [Create Azure Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/how-to-create-template)


### Challenge

We will use GitHub Actions to automate the deployment of our Azure infrastructure. For our application, we will deploy 3 environments: `dev`, `test` and `prod`. Each environment will have its own Web App, however all of our environments will share a single Resource Group, App Service Plan, Application Insights instance, and Azure Container Registry. **NOTE: in real deployments, you will likely not share all of these resources.**


1. Review the ARM template. Notice how it defines a number of parameters and uses them to create the Resource Group, App Service Plan, Web App, Application Insights, and Azure Container Registry. 

2. Update the parameters section of the ARM template, replacing all instances of `<prefix>` with a unique lowercase 5 letter name. The resulting name needs to be globally unique to correctly provision resources. Notice the `webAppName` parameter on line #8 - you will override this placeholder value later when you call the ARM template.

3. Create a service principal to login to Azure (your coach may supply you these credentials and a resource group name or if you have your own Azure subscription you will need to login and create a resource group and then create a service principal with permissions (e.g. contributor) in this group. **Hint: details on creating the service principal can be found in the [Azure/login](https://github.com/Azure/login) README.md** 

4. Create a GitHub repository level secret to store the above login credentials

5. Create a GitHub repository level configuration variable to store the name of the Azure resource group name

6. Create a new GitHub workflow (`deploy.yml`) that runs on a manual trigger (*not* triggered by a push or pull request).

7. Configure your workflow to accomplish the following:
    - Use a service principal to login to Azure using your secret and configuration variable values.
    - Use the "Deploy Azure Resource Manager (ARM) Template" action to call your ARM template in your repo

8. Manually run your workflow. When your workflow completes successfully, go to the Azure portal to see the `dev` environment. **Note: if you were supplied Azure connection details your coach may need to help you see this** 

If everything worked, we are going to call the ARM template again, but override the `webAppName` parameter in the ARM template.

9. Create an environment variable called `targetEnv` in your workflow and set the *value* to the `webAppName` in your ARM template, BUT, replace the "dev" with "test" (i.e., '`<prefix>`devops-test').

10. Update your "Deploy Azure Resource Manager (ARM) Template" action to call your ARM template in your repo and override the `webAppName` parameter with the new `targetEnv` environment variable.

11. Rerun the workflow. When your workflow completes successfully, go to the Azure portal to see the new `test` App Service. **Note: if you were supplied Azure connection details your coach may need to help you see this** 

12. If everything worked, replace the "test" in your `targetEnv` with "prod" and rerun the workflow. When your workflow completes successfully, go to the Azure portal to see the new `prod` App Service. **Note: if you were supplied Azure connection details your coach may need to help you see this** 

You should see now have all three environments in Azure.

### Success Criteria

- Your `deploy.yaml` workflow completes without any errors and overrides the `webAppName` parameter when calling the ARM template.
- Your resource group contains 6 resources: 3 App Services (dev, test, prod), 1 Application Insights, 1 App Service plan and 1 Container registry. 

### Learning Resources

- [What is Infrastructure as Code?](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-infrastructure-as-code)
- [Secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [Variables in GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/variables)
- [Deploy Azure Resource Manager templates by using GitHub Actions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-github-actions)
- [Overriding ARM template parameters](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-cli#parameters)


### Advanced Challenges (optional)

Instead of changing the `targetEnv` variable for each environment that we want to create in the deploy.yaml, you can configure the workflow to prompt the user to enter the environment name before the workflow runs - eliminating the need to hard code the environment name.
- Delete the `targetEnv` environment variable you created earlier.
- Configure your workflow to collect the environment name as a [workflow input](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onworkflow_callinputs) and use that value to override the `webAppName` parameter when calling the ARM template.

**NOTE**: If you are interested in learning more about Infrastructure as Code, there are [multiple](https://github.com/microsoft/WhatTheHack) What the Hacks that cover it in greater depth.

[< Previous](challenge04.md) - [Home](../readme.md) - [Next >](challenge06.md)