# Workflow 2: CD - Deploy to Dev-Test and Production environments

In this workflow we will be focusing on creating a CD workflow. 
Typically, CD deployments are done to production computes hosted in Cloud or in your premise. For our workshop, to simplify the setup complexity, we will be using GitHub Pages hosted in two repos to illustrate the CD concepts. One of the repository will host a Dev-Test instance of the page and the other will host the Production instance of the page.
In real world, the steps and Actions used for illustration can likely be replaced with steps and Actions targeting any hosting services.

At the end of the exercise we will learn - 
1. Using deploy Action from Marketplace
2. Environment variables at workflow level and at individual jobs level
3. Environment secrets and approvals
4. Using environments in the workflow

## Build and deploy to Dev-Test environment

For Dev-Test environment, we will be building the source and deploying to GitHub Pages in the same repo as our source code i.e. `username/ci-cd-with-actions` repo.

1. Create a new workflow file named `cd.yml` in `.github\workflows` folder.
2. Add the name of the workflow as "React App CD"
3. Add triggers for the workflow as `Push` on `main` and `releases/*` branches
4. Next, we will add environment variables at workflow level, related to settings that are common for all instances where we want to deploy, so that these can be used by all the jobs in our workflow. The environement variables we want to define at workflow level are - `owner` and `domain` to construct the urls where our app will be deployed. 

Following is the yaml for the above steps. 💡 Please replace the _username_ in the snippet below. 
```yaml 
# CD workflow for React app

name: React App CD

on:
  push:
    branches:
    - main
    - releases/*

env:
  owner: <username>
  domain: github.io
```
5. Add a new job with id `deploy-dev-test`
   - To run on `ubuntu-latest` 
   - Add a name for the job as "Deploy to Dev-Test environment"
   - We will be using an environment named `Dev-Test` for this job and we will construct the `url` for this environment. As this environment is not already defined by us in this repository, GitHub Actions will take care of creating it during the first run.
   - We also want to define an environment variable at job level for the repository name where we will publish. As the repo name will vary for various deployment environments, so it will vary for each job.
   - Add the following steps to this job
      - Checkout the repository
      - Install and Build for node
      - Deploy to GitHub Pages by searching for an Action from Marketplace. 
         - We will be using JamesIves/github-pages-deploy-action. This action will help in deploying the build folder we specify to a branch we mention.
   
   You can use the following yml snippet -

```yaml

jobs:
  deploy-dev-test:
    name: Deploy to Dev test environment
    runs-on: ubuntu-latest
    environment: 
      name: DevTest
      url: https://${{ env.owner }}.${{ env.domain }}/${{ env.repo }}//
    env:
      repo: actions-workshop
    steps:
      - name: Checkout 
        uses: actions/checkout@v2.3.1
      - name: Install and Build
        run: |
          npm install
          npm run build
      - name: Deploy 
        uses: JamesIves/github-pages-deploy-action@4.1.0
        with:
          branch: gh-pages # The branch the action should deploy to.
          folder: build # The folder the action should deploy.
```
6. Commit the above yml workflow to `main` and observe the run in Actions tab

8. Enable `GitHub Pages` settings to see your app deployed on Dev-Test environment.
   - Go to `Settings` in your repository and click on `Pages`
   - Please ensure that you have chosen `gh-pages` branch and `/(root)` folder. Press save. The settings should like below - 
   <img width="952" alt="image" src="https://user-images.githubusercontent.com/25735209/111984704-b9b16080-8b31-11eb-8f1a-0ab1a813b126.png">


  You will find the url where the app will get deployed. It will be - https://username.github.io/actions-workshop/
  🎉 Open the url and you will find that your app has been deployed to the int environment -
  
  <img width="206" alt="image" src="https://user-images.githubusercontent.com/25735209/112098841-76ee9780-8bc8-11eb-9c03-ba101e1c476c.png">

  
## Deploy to Production environment 

After the dev-test environment has been validated and we are ready for the next stage.
For production environment, we will create a new public repository for deploying to GitHub Pages, so that we have a separate production url where the app will be hosted.

1. Create a new public repository `actions-workshop-prod` in your user account
   - Click on your profile icon on top right corner and open `Your repositories`
   - Press `New` to create a new repository
   - Provide the repo name as `actions-workshop-prod`
   - Ensure to choose `Public`
   - Leave rest options as default and click `Create repository`

2. For production environment, we want to add protection rules by configuraing approvals. Also, as we will be publishing to a separate repository, we will need the access token to deoply to GitHub Pages for that repository. 
So, for adding an approval protection rule and to store the access token as a secret specific to this environment, we will configure a new Environment named `Production` in `ci-cd-with-actions` repository
   - Go to 'Your repositories' from your profile icon and open the `ci-cd-with-actions` repository
   - Open `Settings` tab -> open `Environments`
   - Click on `New environment`
   - Give Name as `Production` and press `Configure environment`
   - Checkmark `Required reviewers` and add your username. Click `Save protection rules`
   - Under `Environment secrets`, click `Add secret`
   - Name your secret `TOKEN`
   - Add the PAT token generated for the scope 'public_repo' the the secret value and click on 'Add secret'. If you have not generated a PAT in the prerequisites, you can use the following steps.

<details>
        <summary><b>Click here for steps to generate PAT to access your public repositories</b></summary>
   - Again go click your profile icon on top right
   - Go to `Settings`
   - Now click on `Developer Settings` from the options listed on the left side
   - Inside Developer Settings, click on `Personal access tokens`
   - Press `Generate new token`
   - Check mark only `public_repo` scope.
   - Add a note in the text box provided and click `Generate token`
   <img width="655" alt="image" src="https://user-images.githubusercontent.com/25735209/111993769-02bae200-8b3d-11eb-83e8-84402b0f8102.png"> 
   Keep this tab open or copy the generated token to a safe place. This needs to used in the environment secret settingccccccujdc
</details>  

   This is how the environment setting should look - 
   <img width="1004" alt="image" src="https://user-images.githubusercontent.com/25735209/111995209-ad7fd000-8b3e-11eb-9eed-0d1b3d8f76b9.png">
 
4. Add a job to deploy to the production environment
   Go to Code and open `.github/workflows/cd.yml` and edit it. Add a job to deploy to staging `deploy-production` 
   - Ensure that this job runs only after `deploy-dev-test` have completed using `needs`. 
   - Use the `Production` environment for this job
   - In the steps 
      - Checkout the repo
      - Download the artifact we uploaded to GitHub Packges in CI workflow
      - Deploy to GitHub Pages for the 
        - `actions-workshop-prod` repository
        - `build` folder
        - `gh-pages` branch
        -  Provide `token` from `Environment secrets`
   
   Add the following job to the workflow file -
   
  ```yaml
  deploy-prod:
    name: Deploy to Production environment
    needs: [deploy-dev-test]
    runs-on: ubuntu-latest
    environment:
      name: Production
      url: https://${{ env.owner }}.${{ env.domain }}/${{ env.prod-repo }}//
    steps:
      - name: Checkout 
        uses: actions/checkout@v2.3.1
      - name: Install and Build
        run: |
          npm install
          npm run build
      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@4.1.0
        with: 
          branch: gh-pages
          folder: build
          token: ${{ secrets.TOKEN }}
          repository-name: ${{ env.owner }}/${{ env.prod-repo }}
   ```
   
   <Following is the complete workflow file content till now>
   
   5. After you commit the above workflow to `main` check the GitHub pages setting in the `actions-demo-staging` repository and open the site - https://username.github.io/actions-demo-staging/ to see your app deployed
    
## See your Workflow in Action!! :tada:

1. Commit a change to `src\App.js`
2. Go to Actions and open the CI-CD workflow
3. Open the latest run to see the details
4. Once the workflow succeeds, go to the GitHub Pages site to see your changes- https://username.github.io/actions-demo-staging/

<Following is the complete workflow file content>
   
## Cleanup - Delete the PAT after the exercise

- Click on your profile icon -> `Settings` -> `Developer Settings` -> `Personal access tokens`
- Delete the Public_repo token created for this workflow.
 
## [Click here for further reference](./further_reference.md)

