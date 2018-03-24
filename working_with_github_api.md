# Building a Commit Bot with the GitHub API

*[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)* is a heavily emphasized principle in software development. It's important that the code written by developers remains as the authoritative source of truth in the codebase and that there is no other. Beyond the code however, *DRY* can also be applied to the repetitive tasks a developer may have to perform on a day to day basis.

At Shopify, we found ourselves manually committing updated development database schema files on a regular basis. The amount of time lost and workflows broken to this repetitive task led us to the need for an commit bot. This article will give an overview of the specification, design and Ruby implementation of a commit bot that leverages the GitHub API.

## Specifications

The solution should:
1) Execute some task to modify a file or files
2) Add and commit the file(s) to a Git branch
3) Schedule the addition and commit of files on a custom interval

## Why GitHub API?

A quick solution could be a cron job, which would execute the task and run `git` commands to add, commit and push the changes to a working tree. The drawbacks however, is that there would need to be a dedicated machine always on standby and ready to execute the cron job. Not very efficient, scalable or reliable.

A more scalable and reliable solution would be to use CI containers so that resources can be spawned when needed. Furthermore, tools such as Buildkite will allow for scheduling build pipelines so that they can be run at given intervals. (They actually accept cron tabs!)

Depending on how CI containers are built (ie. `git clone`), it would be possible to avoid using the GitHub API and run 'git' commands directly within the container. However, many CI containers are built with code only from the current revision, which means that `git` commands are not available within the container. This is where the GitHub API comes in.

## Setting Up in GitHub

A very important first step in setting up is making sure that the bot will be authenticated to the GitHub API. We decided to use GitHub Apps as it allows a limited access to API endpoints that is tailored towards the functionality of the bot. For example, the GitHub App wouldn't be able to make API calls that change the admins of the organization. It is also possible to specify exactly which repository the GitHub App will have access to. With more restrictions, we have less room for unwanted results and failure.

#### Authentication for GitHub Apps

Once a GitHub App is created and installed with the proper permissions for the organization, it needs to be authenticated so that it can gain API access. This [article by GitHub](https://developer.github.com/apps/building-github-apps/authentication-options-for-github-apps/) includes detailed instructions on how to get it done, but the jist of it is to obtain a *PEM* (private key) from GitHub, create a *JSON Web Token* (JWT) and get an access token from GitHub using the JWT. The access token can be used to authenticate the App in the API and looks like:

```JSON
{
  "token": "v1.1f699f1069f60xxx",
  "expires_at": "2016-07-11T22:14:10Z"
}
```

#### Finding a GitHub API Toolkit

For Ruby users, [Octokit](https://github.com/octokit/octokit.rb) is a great project that acts as a toolkit/wrapper for the GitHub API. It provides most of the GitHub API v3 endpoints so you dont have to make the raw HTTP request and parse the result. The sample code shown below will use the Octokit library and I strongly recommend finding a GitHub API toolkit for your language of choice.

## Committing Files with the GitHub API
