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

## Fork this repository

1. Log into your GitHub account and fork this repository <repoUrl>

    - Open this repository Url in a browser
    - Log into your GitHub account
    - Fork
      - This repo contains a starter project for react.js. In our workshop we will automate CI/CD for this starter website.

## Add a CI Workflow

1. In the forked repository, go to Actions tab and press "New workflow" to create a workflow for CI.

3. In the suggested starter workflows, choose "Node.js" -> "Set up this workflow.

4. Name the file as "ci.yml". The file that you are creating on default branch is - 
    - `.github/workflows/ci.yml`
      - This will be the workflow file taking care of building and testing your source code
      - The file content will be empty for now
     
4. Commit this file

6. :tada: Go to Actions tab to see the first workflow running

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
