# Building a Commit Bot with the GitHub API

[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) is a heavily emphasized principle in software development. It's important that the code written by developers remains as the authoritative source of truth in the codebase and that there is no other. Beyond the code however, DRY can also be applied to the repetitive tasks a developer may have to perform on a day to day basis.

At Shopify, we found ourselves manually committing updated development database schema files on a regular basis. The amount of time lost and workflows broken to this repetitive task led us to the need for an commit bot. This article will give an overview of the problem, design and implementation of a commit bot that leverages the GitHub API.

### Problem Specifications
