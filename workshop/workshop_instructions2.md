# Workflow 2: CD workflow - Deploy to GitHub Pages

In this workflow we will be focusing on creating a CD workflow. At the end of the exercise we will learn - 

1. Downloading from GitHub Packages
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

  


