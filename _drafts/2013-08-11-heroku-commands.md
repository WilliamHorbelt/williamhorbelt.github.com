---
layout: post
title: Heroku Commands
category: posts
---

A collection of Heroku commands because my memory is terrible.

git remote add heroku git@heroku.com:project.git
/# Add a Heroku app to a git repo to use git push

git push heroku master
/# pushes a git repo to Heroku

git push --force heroku master
/# force a git push, useful for using a new repo on Heroku

heroku logs -t
/# displays logs, -t is for trailing, or updating output

[How to link a folder with an existing Heroku app](1)
[Replace remote git repo (Heroku)](2)

[1]:http://stackoverflow.com/questions/5129598/how-to-link-a-folder-with-an-existing-heroku-app
[2]:http://stackoverflow.com/questions/8265493/replace-remote-git-repo-heroku