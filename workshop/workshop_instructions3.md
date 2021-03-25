# Workflow 3: Publish package to GitHub Packages

![image](https://user-images.githubusercontent.com/25735209/112420511-556de700-8d53-11eb-90c0-5f0ebea8bcd3.png)

After we have validated the Dev-Test and Production environments, we will want to release our app source as an Npm package for the community to use and build upon. In this exercise we will publish the package to GitHub Packages for the community to consume.

At the end of the exercise we will learn - 
1. Publish packages to GitHub
2. Default env variables
3. Manual triggers (e.g. workflow_dispatch)

## Publish an npm package to GitHub Packages

### Setup our package.json
We need to have settings in the package.json that will determine how our package will be published.
1. Edit the file `package.json` at the root of the repository.
2. The name and the version mentioned here will also be the name and version of the package that will get published
    - The name of the package should be the full name of the current repository including the owner scope(user or organization)
    - You can leave the version as "0.1.0"
    
💡 Please replace the owner in the below json as the user or organization where this repository 
```json
{
  "name": "@owner/ci-cd-with-actions",
  "version": "0.1.0",
}
```

3. Commit the `package.json` file to `main` branch

### Steps to add the workflow 
1. Create a new workflow file named `publish-package.yml` in `.github\workflows` folder.
2. Name the workflow as "Publish npm package"
3. Add a job with id `publish-gpr` that runs on `ubuntu-latest`.
4. Add `workflow_dispatch` trigger, so that we can run the workflow manually.
5. The job should contain the following steps 
    - Checkout the repository
    - Setup Node on the runner with the registry-url as `https://npm.pkg.github.com/`
    - Run `npm publish` to publish the package
6. We will define an env variable `NODE_AUTH_TOKEN` which uses `GITHUB_TOKEN` secret provided by Actions by default. This env will be used by Set up node step to authenticate the GitHub Package Registry.

Following will be the contents for `publish-package.yml` - 

```yaml
name: Publish npm package

on: workflow_dispatch

jobs:

 publish-gpr:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - uses: actions/setup-node@v1
        with:
          node-version: 12
          registry-url: https://npm.pkg.github.com/

      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
7. Commit this workflow file to `main`

### 🎉 See your workflow in action!

1. Go to Actions tab and select the `Publish npm package` workflow.
2. On the right hand side, you will fine `Run workflow` option. Click that and run from `main` branch.
3. Once the run is complete and successul, go to the home page of the repository, where you will find the package that got published under `Packages`

![image](https://user-images.githubusercontent.com/25735209/112415100-39654800-8d49-11eb-96ad-aa1fdda86d1d.png)

You can click the package to see the details about the package and how to consume it.

📓 Please note that you need to update the version in `package.json` everytime you trigger this workflow to publish the package.

### Our software workflow is complete!

With this we have automated the software workflows for our app starting from the changes being done by developers to releasing to the consumers after validation. You can modify these workflows and steps for your team according to specific needs and have CI/CD automated using GitHub Actions.

### [Click here to view the solution yml for Exercise 3](./solution/publish_package.yml)
### [Complete workshop solution](./solution)

## [Further references](./further_reference.md)

