# GitHub Workflow - Terraform Azure
`terraform-azure.yml`

It runs using OIDC and requires you to use the same service principal for both running Terraform and accessing the backend state storage account.

## Example Usage

The following is an example calling workflow when using the default dont use plan outputs mode with an environment and working directory set, running specifically for one repo subdirectory:

```yaml
name: 'Terraform'

on:
  push:
    branches: 
      - main
    paths:
      - "prod-uksouth/**"
  pull_request:
    branches: 
      - main
    paths:
      - "prod-uksouth/**"
      
permissions:
  id-token: write # This is required for requesting the JWT
  contents: read  # This is required for actions/checkout
  
jobs:
  terraform:
    uses: vincishq/github-workflow-terraform/.github/workflows/terraform-azure.yml@v1
    with:
      environment: "Prod UK South"
      working-directory: ./prod-uksouth
    secrets:
      TF_STATE_STORAGE_ACCOUNT_NAME: ${{ secrets.TF_STATE_STORAGE_ACCOUNT_NAME }}
      TF_ARM_CLIENT_ID: ${{ secrets.TF_ARM_CLIENT_ID }}
      TF_ARM_TENANT_ID: ${{ secrets.TF_ARM_TENANT_ID }}
```

The next example is using the advanced mode working with plan outputs and not specifying any additional parameters such as environment or working directory:

```yaml
name: 'Terraform'

on:
  pull_request_target:
    types:
      - closed
    branches:
      - main  
  pull_request:
    branches: 
      - main

permissions:
  id-token: write # This is required for requesting the JWT
  contents: read  # This is required for actions/checkout
  actions: read # This is required to read previous actions workflow runs

jobs:
  terraform:
    uses: vincishq/github-workflow-terraform/.github/workflows/terraform-azure.yml@v1
    with:
      use-tfplan-output: true
    secrets:
      TF_STATE_STORAGE_ACCOUNT_NAME: ${{ secrets.TF_STATE_STORAGE_ACCOUNT_NAME }}
      TF_ARM_CLIENT_ID: ${{ secrets.TF_ARM_CLIENT_ID }}
      TF_ARM_TENANT_ID: ${{ secrets.TF_ARM_TENANT_ID }}
```