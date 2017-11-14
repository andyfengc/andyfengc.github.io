---
layout: post
title:  Best practice of using Git to integrate teamwork
categories: [git, best practice, develop, integration, release]
author: Andy Feng
---

This article introduces GitFlow and GitHub Flow by which we can smartly use Git to integrate codes within team members. It presents two models to manage changes and merge codes. They focus on project development, continuous integration and release. These models origin from Git branching model and feature-oriented. They are based on Git fundamental knowledge such as branch, merge, rebate, squash. 

Please read previous posting for a quick review of Git basic conceptions. 

# GitFlow model #
## introduction ##

[Gitflow](https://github.com/nvie/gitflow) model uses two branches to record the history of the project and multiple feature branches to develop new feature requirements.

- **master branch** stores the official release history
- **develop branch** serves as an integration branch for features
- **feature branch** saves development deliverables

The purpose of separation is to give a clear, highly-focused purpose to each branch. There is no technical distinction between master branch, develop branch and feature branches. 

### Master branch  ###
Each project should have one and only one master branch. All releases should be made only via the master branch. Master branch should always be clean and stable. All development and intermediate operations should never happen in the master branch. This is production branch.

![](/images/posts/20171109-git-13.png)

### Develop branch ###
Develop branch is the integration branch. It reflects the state of latest delivered development changes. This is where any automatic nightly builds (continuous integration) are built from. Develop branch is the fundamentals of master branch. It shouldn't contains any broken code as well. 

![](/images/posts/20171109-git-14.png)

### Feature branch ###
Feature branch includes all development activities such as new features, fixes or special releases. All of them should take place in a dedicated branch - feature branch instead of the master or develop branch. This encapsulation makes it easy for multiple developers to work on a particular feature without disturbing the main codebase.

Feature branch is temporary branch and it is created just for complete a specific requirement. After completion, feature branch should be merged to develop branch and deleted.

![](/images/posts/20171109-git-15.png)

## Workflow##

[Gitflow](https://github.com/nvie/gitflow) is a utility encapsulates branching model of Git. It is very well suited to collaboration and scaling the development team. It is essentially a Git extension including a set of commands to simplify the integration operations.

Some graphical Git clients such as [SouceTree](https://www.sourcetreeapp.com), [GitKraken](https://www.gitkraken.com) have built-in support to GitFlow. 

Here are major steps of GitFlow:

![](/images/posts/20171109-git-20.png)

1. Create a new repo with master branch

1. Create develop branch
	
	**create develop branch from master branch**
	
	`git checkout -b develop master`

	**pull new changes from master to develop**

	`git checkout develop`

	`git merge master`

1. Create a dedicated branch for each new feature

	**create a new feature branch from develop branch **

	`git checkout -b <feature-branch> develop`

1. Do some development in feature branch

1. Constantly pull changes from remote repo/master and merge to feature branch

	`git pull origin <feature-branch>`

1. Merge changes from feature branch to develop branch

	**feature is completed, add commits and merge/rebate changes**

	switch to feature branch: `git checkout feature-branch`

	stage changes: `git add --all`

	commit changes: `git commit --verbose`
	> --verbose option will list all diff result

	switch to develop branch: `git checkout develop`

	merge changes from feature branch: `git merge --no-ff <feature-branch>`	
	> --no-ff: disable the default fast-forward merge

	delete feature branch: `git branch -d <feature-branch>`

	**feature is not completed, stash**
	
	`git stash`

	`git checkout other-feature-branch`

	`do some development`

	`git checkout feature-branch`

	`git stash pop'

	**Create a version tag**
	
	`git tag -a 1.2.1`

1. Continuous integration in develop branch

1. Merge changes from develop branch to master branch and make a release
	
	**merge changes**

	switch to master branch: `git checkout master`

	merge changes from develop branch: `git merge --no-ff develop`
	> --no-ff: disable the default fast-forward merge

	**Create a version tag**
	
	`git tag -a 1.2`

## Summary ##

GitFlow is a classic model and it has a straightforward structure to separate the release and continuous integration. 

However, it is sort of complex and we have to maintain two long-term branches: master and develop. And we have to switch these two branches constantly and it is annoying. There are separate release branch and hotfix branch in serious GitFlow and these conceptions make the model even complex. Also, since master branch is generated from develop branch. There is no big differences between them and we really don't have to maintain two branches in the sense.

# GitHub flow #
GitHub Flow is proposed by GitHub.com. It is a lightweight, branch-based workflow that supports teams and projects where deployments are made regularly. It could be regarded as a simplified version of GitFlow model. 

## Introduction ##
This model has two types of branch:

- **master branch** stores the official release history and also serves as continuous integration 
- **feature branch** saves development deliverables

The main concepts in GitHub Flow are:

- anything in the master branch is deployable
- any new feature or hotfix requirement should be put into dedicated branches
- when we need feedback or help, or we think the branch is ready for merging, open a pull request
- after someone else has reviewed and signed off the feature, we can merge it into master
- Once it is merged and pushed to "master" branch, we should deploy immediately

As we see, instead of directly merging changes from feature branch to develop/master branch, we open a pull request to master branch in GitHub Flow model. This behaviour encourages others discuss and review the changes. Afterwards, this model let us accept and merge changes.

## Workflow ##
Here are major steps of GitHub Flow:

![](/images/posts/20171109-git-35.png)

1. Create a new repo with master branch

1. Create a dedicated branch for each new feature or hotfix

	**create a new feature branch from master branch **

	`git checkout -b <feature-branch> develop`

1. Do some development in feature branch

1. Constantly pull changes from remote repo/master and merge to feature branch

	`git pull origin <feature-branch>`

1. Merge changes from feature branch to develop branch

	**feature is completed, add commits**

	switch to feature branch: `git checkout feature-branch`

	stage changes: `git add --all`

	commit changes: `git commit --verbose`
	> --verbose option will list all diff result

	**feature is not completed, stash**
	
	`git stash`

	`git checkout other-feature-branch`

	`do some development`

	`git checkout feature-branch`

	`git stash pop'

	**Create a version tag**
	
	`git tag -a 1.2.1`

1. Open a pull request to master branch

	![](/images/posts/20171109-git-8.png)

	![](/images/posts/20171109-git-9.png)

	![](/images/posts/20171109-git-10.png)
	
1. Pull request will invite other developers to discuss and review our commits. 
	
	During the review process, we can keep committing new changes in feature branch. If someone comments that we forgot to do something or if there is a bug in the code, we can fix it in feature branch and push up the change. GitHub will show our new commits and any additional feedback.

1. Continuous integration in master branch

1. Merge changes from feature branch to master branch and make a release

	As long as the commit is verified, we can accept the merge to master branch. Then, move on to make a release.

	**delete feature branch: **

	`git branch -d <feature-branch>`

	**Create a version tag**
	
	`git tag -a 1.2`

## Summary ##

GitHub Flow model is a simpler alternative. This model has only feature branches and a master branch. This is very simple and clean. It is also suitable for continuous integration. Master branch is the unique branch to integrate code and make release.

![](/images/posts/20171109-git-36.png)

## FAQ ##

### How to cache changes when switching branches? ###
Typically, when we switch branches, we first commit changes before switching. A new commit point is created in the git log at the moment. 

However, we somehow hope to cache current work instead of committing dirty changes. 

There are two ways:

1. Use `stash`

	stash the changes in current branch: `git stash`
	
	switch to another branch and work on it: `git checkout -b <another-branch>`
	
	pop out the cached changes: `git stash pop`

1. Still use `commit` 

	commit dirty changes as usual: `git commit`

	switch to another branch and work on it: `git checkout -b <another-branch>`

	complete another branch and merge changes to trunk branch: `git commit` `git merge`
	> trunck branch represents main branch and it could be master or develop branch in GitFlow model and master branch in GitHub Flow model

	switch back to previous branch and proceed the previous development: `git checkout <feature-branch>`

	commit the changes after completing development: `git commit`

	squash last dirty commit and new commit when merge to develop branch: 

	`git checkout develop`

	`git merge --squash <feature-branch>`

	> squash is an option to combine multiple commits to one commit when merging changes

	or squash last dirty commit and new commit within branch: `git rebase -i`
	
### How to tag release versions? ###
Tagging in Git is a great way to denote specific release versions of our code. We can create a tag with the current state of the branch we are currently on.

There are two ways to create tags:

- the Git command line

	`git tag <tagname>`

	Please note that when pushing to our remote repo, tags are NOT included by default. We have to explicitly specify that we want to push tags to remote repo:

	Push all tags: `git push origin --tags`

	Push specified tag: `git push origin <tagname>`

	list all tags

	`git tag`

	delete a tag

	`git tag -d <tagname>`
	
- Github's web interface
	1. Click the releases link on our repository page
		![](/images/posts/20171109-git-16.png)
	1. Click on Create a new release or Draft a new release
		![](/images/posts/20171109-git-17.png)
	1. Fill out the form fields, then click Publish release at the bottom
		![](/images/posts/20171109-git-18.png)
		![](/images/posts/20171109-git-19.png)
	1. After create tags on GitHub, we can fetch it into our local repository or pull it
	
## References ##

[Forking Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow)

[Git Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow)

[Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)

[Understanding the Git Workflow](https://sandofsky.com/blog/git-workflow.html)

[http://www.cppblog.com/deercoder/archive/2011/11/13/160007.aspx](http://www.cppblog.com/deercoder/archive/2011/11/13/160007.aspx)

[Understanding the GitHub Flow](https://guides.github.com/introduction/flow/)