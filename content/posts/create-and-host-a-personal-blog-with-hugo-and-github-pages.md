---
title: "Create and host a personal blog with Hugo and GitHub Pages"
---

I have always wanted to create a blog to record my thoughts and notes, but I didn't want to spend a lot of time setting up all kinds of stuff. After some research on what options I had, I found Hugo and GitHub Pages to be the right choices for me to create this blog. By creating this blog, I learned how to use the Hugo and the Git command line. The content involved is shallow, but I was very happy to be able to simply set up a blog.

## So what is Hugo?
Hugo is an open source static website generator that supports Markdown format files and has a huge range of themes to choose from. All this makes the whole process of creating a blog very easy and comfortable. There are many website generators on the market, and the biggest reason I chose Hugo is the abundance of themes for users to choose from! (This is definitely great for people who need a simple static personal blog)

All you need is:
- Create a GitHub account
- Create a static site by installing Hugo
- Choose and install the theme you like
- Customize and set up your theme
- Choose a hosting provider
- Publish your website

## Steps

## Create a new GitHub repository
Fill in the repository name as `<username>.github.io`, keep all settings as default and click `Create repository`.

## Generate SSH key
We need to create an SSH key locally, the purpose of this is to generate a key on your computer to connect with GitHub. Later we can quickly and easily save our own local files to Github and generate website content.

Enter the code below to generate the SSH key (the content in double quotes is the email you used to register your GitHub account)：
```shell
ssh-keygen -t rsa -C "xxx@email.com"
```

In the default directory, find the `id_rsa.pub` file in `.ssh`, open it and copy the contents.

Go back to the browser, go to **SSH and GPG keys** in Github settings, click **New SSH keys**, fill in the title, and paste the content you just copied into the Key's fill box.

Go back to your local Git window and enter the following command to verify that you have successfully connected to GitHub.
```shell
ssh -T git@github.com
```

After success, enter the following command to bind your Github account, and you are done with connecting local and Github.
```shell
git config --global user.name "xxx"
git config --global user.email "xxx@email.com"
```

## Install HomeBrew
> HomeBrew is a package manager for macOS (or Linux): https://brew.sh/

Since I'm using Ubuntu, I'll use the Ubuntu commands as a demo for this tutorial.
First, we need to install the essential build tools for HomeBrew. Execute this command in your local terminal.
```shell
sudo apt-get install build-essential procps curl file git
```
Once that's done, we can install HomeBrew.
```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
We need to add HomeBrew to the `PATH` so that we can execute HomeBrew commands in the terminal at any location.
```shell
test -d ~/.linuxbrew && eval "$(~/.linuxbrew/bin/brew shellenv)"
test -d /home/linuxbrew/.linuxbrew && eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
test -r ~/.bash_profile && echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >> ~/.bash_profile
echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >> ~/.profile
```
You've done it!

## Install Hugo
Execute the commands below to install Hugo.
```shell
brew install hugo
```

## Create a new site
We are going to create a new Hugo site in a folder named **keatylan**.
```shell
hugo new site keatylan
```

## Install a theme
It's time for the exciting step of choosing your favorite set of themes!
Hugo's themes can be viewed on this website at: https://themes.gohugo.io/
In my case I'm using [hello-friend] as my Hugo theme.
```shell
cd keatylan
git clone https://github.com/panr/hugo-theme-hello-friend.git themes/hello-friend
```

## Modify the config.toml file
We have to modify the `config.toml` file in the root directory of our site, and modify the `baseUrl` property to point to our GitHub Pages. Of course, we can also customize the site according to our requirements such as the title and menu.
```shell
baseUrl = "https://keatylan.github.io/"
```

## Build your site
Execute the command after we have tweaked some settings of our `config.toml`. The command output is automatically created and placed in the `./public` directory, and this directory is where we will publish to GitHub.
```shell
hugo
```

## Push your code to GitHub
After building and creating the `./public` directory, we can now push our directory to GitHub by executing a few lines of the Git command.
```shell
cd keatylan/public
git init
git remote add origin git@github.com:keatylan/keatylan.github.io.git
git add .
git commit -m "Initial commit"
git push --set-upstream origin master
```

## Test your webpage
In a few minutes we can try to browse our own website by typing our GitHub Pages link in the web browser. (E.g. `<username>.github.io`)

## Configuring Github Actions to automate deployments
Create a new GitHub repository and name it as `<username>_source`.
A second repository will be responsible for hosting the source code, and we will push changes to that repository every time we want to add a new post.

After we push the changes, GitHub Actions will grab the content from `<username>_source`, build it and publish the output to the original site repository.
First create a `.gitignore` file, since we don't need to push the `./public` directory. After that, we push everything to the dotnetrambligs_source repository.
```shell
cd keatylan
echo "public/" >> .gitignore
git init
git remote add origin git@github.com:keatylan/keatylan_source.git
git add .
git commit -m "Add site files"
git push --set-upstream origin master
```
After that we need to generate a personal access token in the `Developer settings` in GitHub personal settings, you can fill in any name, check the `repo` and `workflow` and click `Generate token`. go back to the settings in our newly created repository and click on the Go back to the settings in our newly created repository, click on `Actions` under `Secrets`, click on `New repository secret`, create a new title of `personal_token` and copy the token you just created.
Click `Actions` under the project and select `set up a workflow yourself` and add the following code:
```shell
name: hugo CI

on:
  push:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true 
          fetch-depth: 1   

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.PERSONAL_TOKEN }}
          external_repository: keatylan/keatylan.github.io
          publish_branch: master
          publish_dir: ./public
```
Submit the code above and see if you committed successfully.

## Congratulations!
After some tinkering with code and setup, our blog is up and running!

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [hello-friend]: https://themes.gohugo.io/themes/hugo-theme-hello-friend/
