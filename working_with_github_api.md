# Building a Commit Bot with the GitHub API

*[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)* is a heavily emphasized principle in software development. It's important that the code written by developers remains as the authoritative source of truth in the codebase and that there is no other. Beyond the code however, *DRY* can also be applied to the repetitive tasks a developer may have to perform on a day to day basis.

At Shopify, we found ourselves manually committing updated development database schema files on a regular basis. The amount of time lost and workflows broken to this repetitive task led us to the need for a commit bot. This article will describe how we set up and leveraged the GitHub API to programatically make commits to the repository.

## Setting Up in GitHub

A very important first step in setting up is making sure that the bot will be authenticated to the GitHub API. We decided to use GitHub Apps as it allows a limited and customized access to API endpoints. Thus, we can [create the GitHub App](https://developer.github.com/apps/building-github-apps/creating-a-github-app/) to only have permissions in managing repository contents and prohibit the app from making administrative organization changes.

#### Authentication for GitHub Apps

Once a GitHub App is created and installed with the proper permissions for the organization, it needs to be authenticated for API access. This involves obtaining a *PEM* (private key) from GitHub, creating a *JSON Web Token* (JWT) and getting an access token from GitHub APIs using the JWT. The generated access token have permissions scoped by the GitHub App and expire after an hour.

```JSON
{
  "token": "v1.1f699f1069f60xxx",
  "expires_at": "2016-07-11T22:14:10Z"
}
```

This [article by GitHub](https://developer.github.com/apps/building-github-apps/authentication-options-for-github-apps/) includes detailed instructions on authenticating GitHub Apps.

#### Finding a GitHub API Toolkit

For Ruby users, [Octokit](https://github.com/octokit/octokit.rb) is a great project that acts as a toolkit/wrapper for the GitHub API. It provides most of the GitHub API v3 endpoints so you dont have to make the raw HTTP request and parse the result. The sample code shown below will use the Octokit library and I strongly recommend finding a GitHub API toolkit for your language of choice.

## Committing Files with the GitHub API

### Committing an Individual File with the GitHub API

Committing a single file with the GitHub API is quite straightforward. GitHub API v3 provides specific [endpoints](https://developer.github.com/v3/repos/contents/#create-a-file) for creating, updating and deleting a file. All one needs to provide is the path of the file, a commit message and the contents, if only committing multiple files was this easy!

### Committing Multiple Files with the GitHub API

Committing multiple files with the GitHub API is much more involved. It's possible to just call the endpoint for adding an individual file but this would pollute the Git history with too many commits over a period of time.
