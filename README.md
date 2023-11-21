# Define ArgoCD projects and their apps (LAB) - argocd-lab-apps

## What is this?

Repo for management of Argo projects and apps for lab environment.

In the [current ArgoCD deployment](https://github.com/trevgall/argocd-lab-deploy) there is a top level project and app that [deploys the contents of this repo](https://github.com/trevgall/argocd-lab-deploy/blob/main/lab/kustomize/base/apps/lab.yaml).

This repo consists of Helm resource definitions that allows on-board/off-board of projects, applications and what environments (clusters) those applications are deployed to.

## How do I on-board/off-board applications/projects?

* Create a branch named something like `feature/new-app-onboard`
* Modify [projects-and-apps/values.yaml](projects-and-apps/values.yaml) to add the new project, app in the desired cluster.
* Run `helm template projects-and-apps` and make sure it works and does what you want
* Commit and push to the branch
* Create a pull request
* After review, merge!
* ArgoCD will update the projects and applications
* Delete the feature branch, after confirming that the changes work in Argo.

## Editing `values.yaml`

In general just copy and paste an existing application/project.

The project name should, ideally, match the GCP project name e.g. if the GCP project name is `payments-{env}` the project name in the `values.yaml` should be `payments`.

You should only specify environments for the application that an application is actually onboarded to. e.g. you have `project1` with two applications `application1` and `application2`. 
* `application1` is onboarded to `dev`
* `application2` is onboarded to `dev` `qal` `pte`

From this the configuration for this project should look something like:
```
- name: "project1"
  description: "Project 1 description"
  applications:
  - name: "application1"
    repoURL: "https://github.com/trevgall/project1-application1-deploy-resources-nonprod.git"
    environments:
    - name: dev
  - name: "application2"
    repoURL: "https://github.com/trevgall/project1-application2-deploy-resources-nonprod.git"
    environments:
    - name: dev
    - name: qal
    - name: pte
```


## How can I see the resources that Argo will deploy?

Try cloning this repo and running:

```
cd argocd-lab-apps
# Build app and project resources for all projects
helm template projects-and-apps
```