---
layout: post
published: false
---

# How My Site Uses Github Pages

To quickly setup my personal site, I pulled an [often used](https://github.com/alshedivat/al-folio) academic website template built on [Jekyll](https://jekyllrb.com/) and configured to deploy on Github Pages. The system works smoothly, but I don't know how to use it beyond the very basics. The combined system uses some technologies I'm not familiar with: 1/ Jekyll, and 2/ Github Pages, and 3/ Github Actions. The goal of this post is to document how the current system works so that I can better use and, possibly, extend it.

## Tools

### Jekyll

### Github Pages

The main [product page](https://pages.github.com/) states that you can host websites directly from your github repository: "Just edit, push, and your changes are live". You can create two kinds of sites: account/organization sites (limited to just one), and unlimited project sites. For account/organization sites, the basic product is straightforward. You create a repository with a specific name (this is important so that Github knows that the repository is intended to be an account site), and then you use the repository as the "root" of your site by commiting an `index.html` file to it. Push the change you commit, and the site will be available.

Pages serves static sites. This means, roughly, that the web server will map paths in the HTTP request to a stored object (could be a file in a file system, a chunk of bytes stored in a database, whatever) and returns the content as-is to the client. Unconfigured, Pages will serve the files saved in a Git repository on a given branch (`master` or `mainline` by default).

In my site, I have two branches: [master](https://github.com/pschulam/pschulam.github.io/tree/master) and [gh-pages](https://github.com/pschulam/pschulam.github.io/tree/gh-pages). The `master` branch contains all the source files that Jekyll uses to generate the site pages, and the `gh-pages` branch contains the Jekyll output that Pages actually serves. These two branches, in general, are called the "source" and the "deploy" branches. To change the deploy branch, go to your repository's `Settings > Pages` and change the branch (there is also additional flexibility to deploy from a specific directory within a given branch). You can directly develop in the deploy branch (in which case there is no source branch), or you can use a build process to translate source artifacts into the final site. My site uses the "source and deploy branches" approach, and uses Github Actions for the build.

### Github Actions

Github Actions is a CI/CD platform. The key concept is a *workflow* which describes a collection of *jobs* that run in response to *triggers*. In many ways, Actions works like the front end to a cluster workload manager such as [Slurm](https://slurm.schedmd.com/documentation.html) or [Kubernetes](https://kubernetes.io/). A workflow is defined using a YAML file and stored in the `.github/workflows` directory of your repo.

A trigger is an event that kicks off the jobs in a workflow. This is usually an event in the repo (e.g. you push a change or merge a pull request), but can also be a scheduled trigger (like a chron job) or a manual trigger.

A job is a sequence of *steps* that are configured to run on a given machine. Each step can either be a script or an *action*, which is a reusable, configurable step. To specify where a job runs, you set the `runs-on` key in the configuration file. Github provides a small number of VM images for public repositories that are free to use. The options are listed [here](https://docs.github.com/en/actions/writing-workflows/choosing-where-your-workflow-runs/choosing-the-runner-for-a-job#standard-github-hosted-runners-for-public-repositories). Actions supports running workflows on larger hosted machines (e.g. one with a GPU) or on self-hosted machines, but I won't cover that here.

For the site setup I've forked, Actions push content from `master` to `gh-pages`. The workflows are defined [here](https://github.com/pschulam/pschulam.github.io/tree/master/.github/workflows). Let's break down how they work. We've got three workflows:
* `[deploy-docker-tag.yml](https://github.com/pschulam/pschulam.github.io/blob/master/.github/workflows/deploy-docker-tag.yml)`: Triggers when we push a tag that matches `v*`. There's only one job. It checks out the repo, sets up an environment to build docker images, then builds the image using the `./Dockerfile` in the repo root. It then pushes to `[amirpourmand/al-folio](https://hub.docker.com/r/amirpourmand/al-folio)` on Dockerhub. I don't ever use this.
* `[deploy-image.yml](https://github.com/pschulam/pschulam.github.io/blob/master/.github/workflows/deploy-image.yml)`: Similar to the `deploy-docker-tag.yml` workflow, but builds whenever we push to `master` and we're the original author (which we're not). The difference between this workflow and `deploy-docker-tag.yml` seems to be that the latter adds some metadata (my guess is the tag).
* `[deploy.yml](https://github.com/pschulam/pschulam.github.io/blob/master/.github/workflows/deploy.yml)`: This is the core workflow that builds my site. It has a single `deploy` job comprised of 5 steps (two actions and three scripts). The two actions (1) checkout the repo and (2) setup ruby. The scripts run afterwards and (1) installs the `mermaid` CLI tool, (2) configures git and sets some state based on the triggering event, and (3) runs the `./bin/deploy` script in the repo. The `./bin/deploy` script is where the bulk of the work happens. In a nutshell, the `./bin/deploy` script will checkout the source branch, build the site, move the `./_site` contents to the repo root (so that Pages will serve the content), create a new local deploy branch (deleting it if it exists), add the built site, then force push to the `origin` remote.

Once the deploy branch is pushed to Github (`remote`), Pages will serve the new content!

## Developing Locally

Using the Actions-driven process I described above isn't great for site development. Making a change takes several minutes to show up on the web. The repo comes with some useful tools for serving a local copy of the content through a docker container.

<!-- Better understanding this system is one of the goals of the blog post (it will help me to better understand 1/ what Github Pages offers, and 2/ the details of how it works).

I want to start writing more regularly and publishing the content. I don't expect people to read what I write, but would like to get in the habit of "shipping" the things I think about.

I've used Github Pages for my personal site, but never taken time to understand 1/ what it offers beyond the basics, 2/ the details of how it works, and 3/ how I can set up my content so that I can easily switch away from Github Pages in the future if I want to (to another hosting solution) -->
