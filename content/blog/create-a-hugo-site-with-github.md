---
title: "Create a Hugo site with Github"
date: 2017-01-15T23:28:25-05:00
categories = ["coding", "markdown"]
---

# Step 1: Install Hugo
For Linux users, you can install hugo through snap or apt-get command in the terminal. See [install] (https://gohugo.io/getting-started/installing) for further details or if you are running other OS. 

```bash
snap install hugo
```
or 
```bash
sudo apt-get install hugo
```

# Step 2: Create a new site

In your terminal, run the following command, where site_name will be your site directory. This means that all the markdown and templates and configuration will be store in that directory. For simplicity, let's call this directory `blog`.

```
hugo new site blog
```

# Step 3: Add a theme
See [themes.gohugo.io](https://themes.gohugo.io/) for a list of themes to consider. In this example, I use Minimo theme developed by Munif Tanjim.

```
cd blog
git init
git submodule add https://github.com/MunifTanjim/minimo themes/minimo
```
This will add Minimo's repository as a submodule to the `blog` repository. Now, we will have to pull the theme:

```
git submodule init
git submodule update
```
Minimo is ready to be used. For getting started with Minimo, copy the config.toml file from exampleSite directory inside Minimo's repository to the `blog` repository.
```
cp themes/minimo/exampleSite/config.toml .
```
Now, we can start editing this file and change configuration! For more setup guides on the theme, go to [minimo] (https://minimo.netlify.com/)

# Step 4: Add some content

```
mkdir content/blog
hugo new blog/my-first-post.md
```
We can open the file `my-first-post.md` in `content` directory to input some content. Now, we can start the Hugo server wit drafts enabled:

```
hugo server -D

Started building sites ...
Built site for language en:
1 of 1 draft rendered
0 future content
0 expired content
1 regular pages created
8 other pages created
0 non-page files copied
1 paginator pages created
0 categories created
0 tags created
total in 18 ms
Watching for changes in /Users/username/projects/blog/{data,content,layouts,static,themes}
Serving pages from memory
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop

``` 
Open with any web browsers at <http:localhost:1313//>

# Step 5: Deploy on github
There are 2 types of Github Pages: 
* User/Organization Pages
* Project Pages

In this example, I only focus on creating a User/Organiztion Pages site. 

### Set up repositories on Github
First, create two new repositories on Github: <br/>
1. `blog` <br/>
2. `<username>.github.io` Make sure to check the _Initialize this repository with a README_ box, since that will make the next step easier

Delete the `public/` directory from git if it exists:

```
git rm -r public
```

### Add the submodule
Clone the `<username>.github.io` repo into a submodule in `public`:
```
git submodule add git@github.com:<username>/<username>.github.io.git public
```

Add everything and push it up to Github:


```
# Build the project
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`
# Go to the public folder
cd public

# Add changes to git.
git add .
git commit -m 'Generate the site'
git push -u origin master
```

### Automate deploying
We can add a `deploy.sh` script to automate the preceding steps and make it executable with `chmod +x deploy.sh`

```
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public
# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back up to the Project Root
cd ..
```

To deploy, run `./deploy.sh "Your optional commit message"` to send changes to `<username>.github.io`. Note that we still need to commit changes to our `blog` repository.