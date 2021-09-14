# Cloud.gov CF CLI Tools

A Github action for using CF CLI tools while deploying and managing apps on [cloud.gov](https://cloud.gov).

## Usage

Follow the instructions for setting up a [cloud.gov service account](https://cloud.gov/docs/services/cloud-gov-service-account/). Store you username (CG_USERNAME) and password (CG_PASSWORD) as [encrypted secrets](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets). 

## Sample workflow

The following is an example of a workflow that uses this action. This example shows how to deploy a simple .NET Core app to cloud.gov

```yml
name: .NET Core Deploy

on:
  pull_request:
    branches: [ {branch-name} ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Setup .NET Core
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 3.1.101

    - name: Install dependencies
      run: dotnet restore
      
    - name: Build
      run: dotnet build 
      
  deploy:
    runs-on: ubuntu-latest
    needs: build
    
    steps:
      - uses: actions/checkout@v2
      - name: Deploy to cloud.gov
        uses: cloud-gov/cg-cli-tools@main
        with: 
          cf_api: https://api.fr.cloud.gov
          cf_username: ${{ secrets.CG_USERNAME }}
          cf_password: ${{ secrets.CG_PASSWORD }}
          cf_org: your-org
          cf_space: your-space
          cf_command: push

```

You can optionally add the name of a manifest file (default is `manifest.yml`), use a `vars.yml` file with your push, or specify a command to run instead of `cf push` (e.g., `cf push APP_NAME --strategy rolling` for a zero downtime deploy).

## Other options

There are other tools and utilities that you can use to deploy your application to cloud.gov. Here is a list of some of the more common options.

* https://github.com/18F/cg-deploy-action
* https://github.com/usds/cloud-gov-cli
* https://github.com/alphagov/paas-docker-cloudfoundry-tools 
* https://hub.docker.com/r/governmentpaas/cf-cli 