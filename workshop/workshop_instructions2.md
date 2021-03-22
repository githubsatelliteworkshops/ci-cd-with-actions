# Workflow 2: CD workflow - Deploy to GitHub Pages

In this workflow we will be focusing on creating a CD workflow. At the end of the exercise we will learn - 

1. Downloading artifacts from GitHub Packages
2. Using deploy Action from Marketplace
3. Environments secrets and approvals
4. Using environments in the workflow

## Build and deploy to int environment for testing

For int environment, we will be building the source and deploying to GitHub Pages in the same repo.

1. We can enhance the same workflow file by renaming it to `ci-cd.yml`.

2. Add a new job `build-and-deploy-int`to run on `ubuntu-latest`

3. Add the following steps - 
   - Checkout the repository
   - Install and Build for node
   - Deploy to GitHub Pages by searching for an Action from Marketplace. 
      - We will be using JamesIves/github-pages-deploy-action. This action will help in deploying the build folder we specify to a branch we mention.
   
   You can use the following yml snippet -

```yaml

build-and-deploy-int:
    name: Deploy int environment
    runs-on: ubuntu-latest
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
          BRANCH: gh-pages # The branch the action should deploy to.
          FOLDER: build # The folder the action should deploy.

```

4. Enable `GitHub Pages` settings
  - Go to `Settings` in your repository and click on `Pages`
  - Please ensure that you have chosen `gh-pages` branch and `/(root)` folder. Press save. The settings should like below - 
  <img width="952" alt="image" src="https://user-images.githubusercontent.com/25735209/111984704-b9b16080-8b31-11eb-8f1a-0ab1a813b126.png">


  You will find the url where the app will get deployed. It will be - https://<username>.github.io/actions-workshop/
  🎉 Open the url and you will find that your app has been deployed to the int environment -
  
  <img width="690" alt="image" src="https://user-images.githubusercontent.com/25735209/111984902-f8471b00-8b31-11eb-9a17-93912e303c54.png">

  
## Download the deployable artifact from GitHub Packages and deploy to staging environment

For staging environment, we will create a new public repository and publish a branch from this to GitHub Pages to simulate a separate stage environment.

1. Create a new public repository `actions-demo-staging` in your user account
   - Click on your profile icon on top right corner and open `Your repositories`
   - Press `New` to create a new repository
   - Provide the repo name as `actions-demo-staging`
   - Ensure to choose `Public`
   - Leave rest options as default and click `Create repository`

2. Create a PAT for access to this repository 
   - Again go click your profile icon on top right
   - Go to `Settings`
   - Now click on `Developer Settings` from the options listed on the left side
   - Inside Developer Settings, click on `Personal access tokens`
   - Press `Generate new token`
   - Check mark only `public_repo` scope.
   - Add a note in the text box provided and click `Generate token`
   <img width="655" alt="image" src="https://user-images.githubusercontent.com/25735209/111993769-02bae200-8b3d-11eb-83e8-84402b0f8102.png">
   
   Keep this tab open or copy the generated token to a safe place. we will be using this token to add a secret in the next step
   
 3. Create staging environment in `actions-workshop` repository
   - Go back to 'Your repositories' from your profile icon and open the `actions-workshop` repository
   - Open `Settings` tab -> open `Environments`
   - Click on `New environment`
   - Give Name as `staging` and press `Configure environment`
   - Checkmark `Required reviewers` and add your username. Click `Save protection rules`
   - Under `Environment secrets`, click `Add secret`
   - Name your secret `TOKEN`
   - Add the PAT token generated in previous step in the the secret value and click on 'Add secret'
   
   This is how the environment setting should look - 
   <img width="1004" alt="image" src="https://user-images.githubusercontent.com/25735209/111995209-ad7fd000-8b3e-11eb-9eed-0d1b3d8f76b9.png">
 
 4. Add a job to deploy to the staging environment
   - Go to Code and open `.github/workflows/ci.yml` and edit it
   - Add a job to deploy to staging like we did for int but this time we want to download the deployable artifact from 
    
  
   
   
## Delete the PAT after the exercise
 

