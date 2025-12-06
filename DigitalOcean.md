# GitHub Workflow - Terraform Digital Ocean
`terraform-digitalocean.yml`

The workflow uses S3 (designed for Digital Ocean Spaces) for Terraform state backend storage and authenticates using access keys.

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
    uses: vincishq/github-workflow-terraform/.github/workflows/terraform-digitalocean.yml@v1
    with:
      environment: "Prod UK South"
      working-directory: ./prod-uksouth
    secrets:
      TF_STATE_S3_BUCKET_NAME: ${{secrets.TF_STATE_S3_BUCKET_NAME}}
      TF_STATE_S3_ACCESS_KEY_ID: ${{secrets.TF_STATE_S3_ACCESS_KEY_ID}}
      TF_STATE_S3_SECRET_ACCESS_KEY: ${{secrets.TF_STATE_S3_SECRET_ACCESS_KEY}}
      DIGITALOCEAN_TOKEN: ${{secrets.DIGITALOCEAN_TOKEN}}
```

The next example is using the advanced mode working with plan outputs and not specifying any additional parameters such as environment or working directory but additionally providing DO spaces API authentication:

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
    uses: vincishq/github-workflow-terraform/.github/workflows/terraform-digitalocean.yml@v1
    with:
      use-tfplan-output: true
    secrets:
      TF_STATE_S3_BUCKET_NAME: ${{secrets.TF_STATE_S3_BUCKET_NAME}}
      TF_STATE_S3_ACCESS_KEY_ID: ${{secrets.TF_STATE_S3_ACCESS_KEY_ID}}
      TF_STATE_S3_SECRET_ACCESS_KEY: ${{secrets.TF_STATE_S3_SECRET_ACCESS_KEY}}
      DIGITALOCEAN_TOKEN: ${{secrets.DIGITALOCEAN_TOKEN}}
      SPACES_ACCESS_KEY_ID: ${{secrets.SPACES_ACCESS_KEY_ID}}
      SPACES_SECRET_ACCESS_KEY: ${{secrets.SPACES_SECRET_ACCESS_KEY}}
```