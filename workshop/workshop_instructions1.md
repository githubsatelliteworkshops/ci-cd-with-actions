# Satellite Workshop Instructions: CI/CD with GitHub Actions

 ![image](https://user-images.githubusercontent.com/25735209/112305126-56a40300-8cc4-11eb-95de-5493c1bd8689.png)

**Workflow 1** will focus on creating a CI workflow for our [react app](react-app.md). 
At the end of this exercise we will learn -
1. How to create a new CI workflow 
2. About Workflow templates 
3. Using Intellisense in UI Action authoring  
4. Configuring Action triggers and type of triggers 
5. About Job strategies 
6. Adding a status badge for a workflow

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

## Make changes to the CI workflow to customize for our scenario.

1. Go to `.github/workflows/ci.yml` and enter edit mode by clicking the pencil :pencil: icon
   - To make the workflow name specific to our app, change the name of the workflow to "React App CI".
   - For our app, we want to trigger the CI on push and pull_request events not only for main branch but also all the branches starting with `releases\`. Let's specify these in `on:`
   - Rename the `build` job id to `build-test` as it is does both build and test
   - Provide a name also for this job as "Build and Test". When you start typing "name", use Ctrl+space in the yaml editor for Intellisense. 
           
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
        name: Build and test

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

## Create a Pull request to add a status badge to ReadMe
1. In the `Actions` tab, click on the `CI` workflow and you will find a `Create status badge` option. 
2. Click that and `Copy the status badge markdown`
3. Edit the ReadMe file and paste this markdown on the top of the file. 
4. Commit the readMe file to a new branch and create a pull request.
5. Go to the Pull request and :tada: observe the CI being run in `Checks` in PR conversation tab.
6. After completing the PR, the CI status badge will be there on the repository's home page.
<img width="499" alt="image" src="https://user-images.githubusercontent.com/25735209/111973992-11959a80-8b25-11eb-92e2-fd6cb4298bb3.png">

## [Click here to get started with Workflow 2](./workshop_instructions2.md)
