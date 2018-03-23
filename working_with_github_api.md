# Building a Commit Bot with the GitHub API

[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) is a heavily emphasized principle in software development. It's important that the code written by developers remains as the authoritative source of truth in the codebase and that there is no other. Beyond the code however, DRY can also be applied to the repetitive tasks a developer may have to perform on a day to day basis.

At Shopify, we found ourselves manually committing updated development database schema files on a regular basis. The amount of time lost and workflows broken to this repetitive task led us to the need for an commit bot. This article will give an overview of the specification, design and Ruby implementation of a commit bot that leverages the GitHub API.

## Specifications

The solution should:
1) Execute some task to modify a file or files
2) Add and commit the file(s) to a Git branch
3) Schedule the addition and commit of files on a custom interval

## Why GitHub API?

A quick solution could be a cron job, which would execute the task and run 'git' commands to add, commit and push the changes to a working tree. The drawbacks however, is that there would need to be a dedicated machine always on standby and ready to execute the cron job. Not very efficient, scalable or reliable.

A more scalable and reliable solution would be to use CI containers so that resources can be spawned when needed. Furthermore, tools such as Buildkite will allow for scheduling build pipelines so that they can be run at given intervals. (They actually accept cron tabs!)

Depending on how CI containers are built (ie. `git clone`), it would be possible to avoid using the GitHub API and run 'git' commands directly within the container. However, many CI containers are built with code only from the current revision, which means that `git` commands are not available within the container. This is where the GitHub API comes in.

## Setting Up in GitHub

