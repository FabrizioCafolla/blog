---
title: Deploy article with GitHub workflow on dev.to and medium.com
description: We'll see how to create a pipeline on GitHub that releases articles written in markdown on dev.to and medium.com
tags: devops, tutorial, github, writing
license: all-rights-reserved
published: true
---

# Deploy article with GitHub workflow on dev.to and medium.com

### Intro

This article was born from the laziness that usually surrounds us, programmers. 

Let's be clear, it's constructive laziness, where the core concept is:

If you have to do it more than once automate the process...

My need is to be able to write an article in the fastest and easiest way, but also be replicable on multiple platforms.
So I started looking for a way that using a Markdown file containing the article I could publish it on dev.to and medium.com.

Fortunately, as often happens, someone thought of it before me and implemented a solution.
The beauty of open source!

https://github.com/protiumx/blogpub/tree/v0.4.1

![image](./preview.jpg)

### Implementation 

You can look at the repository containing the sources of the articles

https://github.com/FabrizioCafolla/blog

The structure is very simple inside the blog folder there are all my articles written with .md extension, for example:

```
    ---
    title: Example
    description: Exmpale desc. 
    tags: tag1, tag2, tag3, tag4, tag5
    ---

    # Title

    Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book.

    ![image](./images/img.png) ==> ![image](https://raw.githubusercontent.com/<owner>/<repo>/<articles_folder>/images/img.png)
```

Remember inside the file you need the header (for more info [see here](https://github.com/protiumx/blogpub#articles-configuration)) then you can use all the features of the markdown including the images, which in case they refer to an image in the repository, will be rendered as in the example

Now to have the articles deployed with pipeline on both platforms you have to create the file inside your repository in the following path **.github/workflows**, the name of the file is indifferent, and inside it you have to insert:

```
name: Publish articles

on:
  push:
    branches: [main]

jobs:
  publish:
    name: Publish blog
    runs-on: ubuntu-20.04   
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      
      - name: Deploy
        uses: protiumx/blogpub@v0.4.1
        with:
          articles_folder: %PATH%
          devto_api_key: ${{ secrets.DEVTO_TOKEN }}
          gh_token: ${{ secrets.GITHUB_TOKEN }}
          medium_token: ${{ secrets.MEDIUM_TOKEN }}
          medium_user_id: ${{ secrets.MEDIUM_ID }}
```

Note well to insert instead of %PATH% the path where the articles are contained, in my case is simply **posts**

Once you have created and uploaded your repository on GitHub you have to insert the secrets so that the pipeline can connect to the API of the platforms. Then enter the following keys:

- **DEVTO_TOKEN**: [generate token](https://dev.to/settings/account) on dev.to
- **MEDIUM_TOKEN**: [generate token](https://help.medium.com/hc/en-us/articles/213480228-Get-an-integration-token-for-your-writing-app) on medium
- **MEDIUM_ID**: [get user id](https://github.com/Medium/medium-api-docs#31-users) medium from API 


Once you have inserted the secrets and written the articles the deployment pipeline will be executed only by main you can change it to your liking.

### Considerations

The first consideration is that I still don't understand well how the update of the articles works in case of modification after the publication.

A second consideration is that using this process from a single source I can publish on multiple platforms simply and effortlessly, if one day I wanted to publish on other sites I would just change the pipeline.