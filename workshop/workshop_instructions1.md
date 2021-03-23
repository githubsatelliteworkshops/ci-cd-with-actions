# Satellite Workshop Instructions: CI/CD with GitHub Actions

<Add a diagram to show workflow 1 and workflow 2>

**Workflow 1** will focus on creating a CI workflow for our [react app](react-app.md). 
At the end of this exercise we will learn -
1. How to create a new CI workflow 
2. About Workflow templates 
3. Using Intellisense in UI Action authoring  
4. Configuring Action triggers and type of triggers 
5. About Job strategies 
6. Publishing to Packages  

**Workflow 2** will focus on creating a CD workflow 

:bookmark: The following instructions will take on the assumption that you will be completing the steps on the GitHub UI. Feel free to use the terminal or any other GUIs you are comfortable with.


# Workflow 1: Steps to set up CI workflow

## Fork actions-workshop repository

1. Log into your GitHub account and fork [actions-workshop repo](https://github.com/kaverma/actions-workshop). 

    - Open the [actions-workshop repo](https://github.com/kaverma/actions-workshop) in a browser
    - Sign in to your GitHub account from top right if not signed-in already.
    - Fork this repository into your user account (using `Fork` option on top right of the screen). This repository will get forked as `<username>/actions-workshop`
    
    This repo contains a starter project for react.js for which we will be automating CI and CD flow. It also contains the intsructions for the workshop.
  

## Add a CI Workflow

1. In the forked `<username>/actions-workshop` repository, go to **Actions** tab and you will see a page to "Get started with GitHub Actions". You will find suggested starter workflows for this repository here.

2. Choose **Node.js** -> "Set up this workflow"
<img width="434" alt="image" src="https://user-images.githubusercontent.com/25735209/111957403-f9685000-8b11-11eb-98a3-b3782e813550.png">

3. You can choose to name the file as "ci.yml". The file that you are creating on `main` branch is - 
    - `.github/workflows/ci.yml`
      - This will be the workflow file taking care of building and testing your source code
    - Observe the components of the workflow file
        - `name:` Name of the workflow
        - `on:` Trigger for the workflow
        - `jobs` Only one job is there -> `build`
        `build` job has 
        - `runs-on` 
        - `strategy matrix`
        - `steps` to checkout the repo, setup Node, install denedencies, build and test.
     
4. Use `Start Commit` option to commit this file as such directly to the `main` branch.

5. :tada: Go to Actions tab to see the first workflow running
    - Under `All workflows`, you will find your new workflow `Node.js CI.
    - Select the workflow to see the list of runs. 
    - Open the top most run that got triggered and you can click on the `Matrix: build` to see the jobs running or completed.
    <img width="358" alt="image" src="https://user-images.githubusercontent.com/25735209/111958686-8a8bf680-8b13-11eb-8a93-f77d87558af8.png">

6. Make changes to the workflow to customize for our scenario.
  
   Go to `.github/workflows/ci.yml` and enter edit mode by clicking the pencil :pencil: icon
   - To make the workflow name specific to our app, change it to "React App CI".
   - For our app, we want to trigger the CI on push and pull_request events not only for main branch but also all the branches starting with `releases\`. Let's specify these in `on:`
   - Rename the `build` job to `build-test` as it is does both build and test
           
   💡 When you are editing your workflow, on the right hand side you will find `Marketplace` and `Documentation` tabs. You can check the documentation tab for information on how to make these changes in the workflow 
   
   <details>
        <summary><b>Click here to view the contents of the yaml file to copy:</b></summary>

   ```yaml
   name: React App CI

    on:
      push:
        branches: 
        - main 
        - releases/*
      pull_request:
        branches: 
        - main 
        - releases/*

    jobs:
      build-test:

        runs-on: ubuntu-latest

        strategy:
          matrix:
            node-version: [10.x, 12.x, 14.x, 15.x]
            # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

        steps:
        - uses: actions/checkout@v2
        - name: Use Node.js ${{ matrix.node-version }}
          uses: actions/setup-node@v1
          with:
            node-version: ${{ matrix.node-version }}
        - run: npm ci
        - run: npm run build --if-present
        - run: npm test
        
   ```
   </details>
   - :warning: `yaml` syntax relies on indentation, please make sure that this is not changed

## Upload the build artifact to GitHub Packages
Upload the build artifact that has been generated in this CI to GitHub packages so this can be consumed or deployed further.
1. Add another job in the workflow to upload build directory to GitHub Packages.
    - Edit `.github/workflows/ci.yml` workflow file
    - Add a job `upload-artifact` 
    - Run on `ubuntu-latest`
     
2. Use an Action from Marketplace to upload the build directory to GitHub Packages.
    - When you are editing the `.github/workflows/ci.yml` workflow file, on right hand side you have Marketplace where you can search for available Actions and look a the documentation inline to see how to use that Action in your workflow. We will be using `upload-artifact` Action in this workflow
- Use the following 
```yaml
      upload-artifact:
        needs: build-test
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: npm install and build
          run: |
            npm install
            npm run build
        - uses: actions/upload-artifact@v2
          with:
            name: release-artifact
            path: build/
  ```
3. We need to make sure this job runs only after the previous build-and-test job has completed successfully, for this we will use `needs` in this job which has been added in the previous step

   <details>
        <summary><b>Click here to view the full contents of the yaml file to copy:</b></summary>
   
    ```yaml

        # This workflow will do a clean install of node dependencies, build the source code and run tests across different versions of node
        # For more information see: https://help.github.com/actions/language-and-framework-guides/using-nodejs-with-github-actions

        name: CI

        on:
          push:
            branches: [ main, 'releases/*' ]
          pull_request:
            branches: [ main, 'releases/*' ]

        jobs:

          build-test:
            runs-on: ubuntu-latest

            strategy:
              matrix:
                node-version: [10.x, 12.x, 14.x, 15.x]
                # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

            steps:
            - uses: actions/checkout@v2
            - name: Use Node.js ${{ matrix.node-version }}
              uses: actions/setup-node@v1
              with:
                node-version: ${{ matrix.node-version }}
            - run: npm ci
            - run: npm run build --if-present
            - run: npm test

          upload-artifact:
            needs: build-test
            runs-on: ubuntu-latest
            steps:
            - uses: actions/checkout@v2
            - name: npm install and build
              run: |
                npm install
                npm run build
            - uses: actions/upload-artifact@v2
              with:
                name: release-artifact
                path: build/
     ```
   </details>
   - :warning: `yaml` syntax relies on indentation, please make sure that this is not changed
   
   - Commit this file and look at the Actions tab to see the workflow running.
   After the workflow completes, you will be seeing that an artifact `release-artifact` has been published 
   <img width="1142" alt="image" src="https://user-images.githubusercontent.com/25735209/111973319-61c02d00-8b24-11eb-8fc9-31551537b2eb.png">

## Test the CI Workflow

1. Commit changes to `src\App.js` to the `main` branch
2. Validate that CI workflow has triggered for this change
3. Validate the successful completion of the CI workflow 
4. You can click on the `release-artifact` to download and see the contents are ready to be deployed.

## Additional Exercise - Add a status badge to ReadMe
1. In the `Actions` tab, click on the `CI` workflow and you will find a `Create status badge` option. 
2. Click that and `Copy the status badge markdown`
3. Edit the ReadMe file and paste this markdown on the top of the file. 
4. Commit the readMe file and :tada: observe the CI status badge on teh repo home page
<img width="499" alt="image" src="https://user-images.githubusercontent.com/25735209/111973992-11959a80-8b25-11eb-92e2-fd6cb4298bb3.png">

## [Click here to get started with Workflow 2](./workshop_instructions2.md)
