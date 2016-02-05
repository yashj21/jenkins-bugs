# [JENKINS-11337](https://issues.jenkins-ci.org/browse/JENKINS-11337) Multiple branches ignored

A [git-flow branching workflow](http://nvie.com/posts/a-successful-git-branching-model/)
was not detecting changes when Jochen Ulrich defined "Branches to Build" as
"**" and added the additional behavior to prune stale remote-tracking
branches.

Steps I took trying to duplicate the problem:

* Install git-flow on my Ubuntu development machine
* Initialize the git-flow branching
```
# Create a master branch
git checkout -b JENKINS-11337/master

# Create a develop branch
git checkout -b JENKINS-11337/develop

# Initialize git flow branching (answers in comment)
git flow init
# [gitflow "branch"]
# 	master = JENKINS-11337/master
# 	develop = JENKINS-11337/develop
# [gitflow "prefix"]
# 	feature = JENKINS-11337/feature/
# 	release = JENKINS-11337/release/
# 	hotfix = JENKINS-11337/hotfix/
# 	support = JENKINS-11337/support/
# 	versiontag = JENKINS-11337-
```
* Start a feature branch named bug-check-1
```
git flow feature start bug-check-1
```
* Finish the feature bug-check-1
```
git flow feature finish bug-check-1
```
* Release the feature
```
git flow release start my-bug-check-release-1.0.0
git flow release publish my-bug-check-release-1.0.0
git flow release finish my-bug-check-release-1.0.0
git push --tags
```
* Create a feature branch for my-bug-check-2
```
git flow feature start my-bug-check-2
```
* Make needed changes on that branch
* Finish the feature branch
```
git flow feature finish my-bug-check-2
```
* Release the my-bug-check-2 feature
```
git flow release start bug-check-release-1.0.1
git flow release publish bug-check-release-1.0.1
git flow release finish bug-check-release-1.0.1
git push --tags
```
