# Satellite Workshop Instructions: CI/CD with GitHub Actions

<Add a diagram to show workflow 1 and workflow 2>

**Workflow 1** will focus on creating a CI workflow for our [app](Need link to this). 
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

6. Make changes to the workflow file to change the name of the workflow and change the trigger for the workflow
   Goto yml file we created `.github/workflows/ci.yml` in `Code` tab and edit.
   - Change the name to "CI"
   - Add another trigger for the workflow. The workflow should trigger for push and pull_request against branches starting with `releases\`
           
   Please refer to [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#on)
   
   <details>
        <summary><b>Click here to view the `on` trigger contents to copy:</b></summary>

        ```yaml
        on:
  push:
    branches: [ main, 'releases/**' ]
  pull_request:
    branches: [ main, 'releases/**' ]
        ```
        </details>

6. Change the trigger of the workflow to - 

7. Change matrix strategy to -

9. Upload the build directory to GitHub packages so this can be consumed or deployed. 

10. Ensure that uploading of package happens only after the build and test step has succeeded for all node versions we are testing for

11. Add status badge

- **Knowledge Check**

  - How many jobs are there in the workflow?
    <details><summary><b>Answer</b></summary>
   
    </details>


:tada: Awesome, now our CI workflow should be complete!

## Test the CI Workflow

1. Make changes

## [Click here to get started with Workflow 2](./workshop_instructions2.md)
