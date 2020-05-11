# Introduction 
This docker image can be used to spawn a sibling Docker Container in a Azure Devops Agent,

# Getting Started
1.	Setup your Release Pipeline
2.	Ensure you choose a build Agent that has Docker Socket capability
3.	Configure a pipeline task to execute Ansible with your playbooks
4.  Azure Agent must be exposing Docker Socket - To spawn sibling containers


# PipelineAzure  CI Task 2.x - Inline Script Sample
`
set -x
subscription=$(az account show --query name | sed -e  "s/\"//g")

docker run --rm -v $(System.DefaultWorkingDirectory)/rangerrom/logstash:/playbooks/ <containerregistry>/<imagename> \
 "cd  /playbooks/ansible; ansible-playbook --version; az login --service-principal --username $servicePrincipalId --password $servicePrincipalKey --tenant $tenantId; az account set --subscription $subscription;ansible-playbook logstash-playbook.yaml -i inventory_$(env)_azure_rm.yml --extra-vars \"ansible_ssh_pass=$(clientpassword)\""
`

# Sample Azure Devops Release Pipeline
variables:
  env: 'prod'

steps:
- task: AzureCLI@2
  displayName: 'Azure CLI '
  inputs:
    azureSubscription: 'Romiko-Dev'
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
     set -x
             
     docker run --rm -v $(System.DefaultWorkingDirectory)/rangerrom/logstash:/playbooks/ romiko/ansible:latest \
      "cd  /playbooks/ansible; ansible-playbook --version; az login --service-principal --username $servicePrincipalId --password $servicePrincipalKey --tenant $tenantId; az account set --subscription $subscription;ansible-playbook logstash-playbook.yaml -i inventory_$(env)_azure_rm.yml --extra-vars \"ansible_ssh_pass=$(clientpassword)\""
    addSpnToEnvironment: true
    workingDirectory: '$(System.DefaultWorkingDirectory)/rangerrom/logstash/ansible'`


