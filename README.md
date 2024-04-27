### This document covers the following:
- Setting Git and SSH


## Setting Git and SSH
This section focuses on the core git settings, such as global definitions and setting SSH with your Github account.
All the settings in the sections are done through the command line. Let's start by checking the `git` version running
the following:

``` shell
git --version
```

If this is anew computer or you did not set it before, it should prompt a window and ask you if you want to install
the `command line developer tools`. The `command line developer tools` is required to run git commands. Once installed,
we can go back and set the global settings.


# Set Git global options
Git enables setting both local and global options. The global options will be used as default settings any time triggering
a new repository with the `git init` command. You can override the global settings on specific repo by using local settings. Below, we will define the following global settings:

- Git user name
- Git user email
- default branch name
- Global git ignore file
- Default editor (for merging comments)

# Set git user name and email by using the `config --global` command:

``` shell
git config --global user.name "kalyanbrata"
git config --global user.email "maityk007@gmail.com"
```

# Set default brnach name
Next, let's set the default branch name as `main` using the `init.defaultBranch` argument:

``` Shell
git config --global init.defaultBranch main
```

# Set global Git ignore file
The global `.gitignore` file enables you to set general ignore roles that will apply automatically to all repositories
in your machine. This is useful when having repetitive cases of files you wish to ignore by default. A good example on 
Mac is the system file `.DS_Store`, which is auto-generated on each folder, and you probably do not want to commit it.

First, let us create the global `.gitignore` file using the `touch` command:

``` Shell
touch ~/.gitignore
```

Next, let's define this file as global:

``` Shell
git config --global core.excludesFile ~/.gitignore
```

Once the global ignore file is set, we can start adding the files we want git to ignore systematically. For example,
let's add the `.DS_Store` to the global ignore file:

``` Shell
echo .DS_Store >> ~/.gitignore
```

NOTE: You want to be careful about the files you add to the global ignore file. Unless it is applicable to all cases, sucj as the .DS_Store example, you should not add it to the global settings and define it locally to avoid a git disaster.


# Set default editor
Git enables you to set the default Shell code editor to create or edit commit messages with the `core.editor` argument. Git supports the main command line editors such as `vim`, `emacs`, `nano`, etc. I set main editor as `Vim`:

``` Shell
git config --global core.editor "vim"
```

## Review and modify global config settings
By default, all the global settings saved to the `config` file under the `.ssh` folder. You can review the saved settings, modify and add new ones manually by editing the `config` file:

``` Shell
vim ~/.gitconfig
```

# Set SSH with Github
Setting `SSH` key is required to sync your local git repositories with the `origin`. By default, when creating the SSH keys it writes the files under the `.ssh` folder, if exists, otherwise it writes it down under the root folder. It is more "clean" to have it under the `.ssh` folder, therefore, my settings below assume this folder exists.

Let's start by creating the `.ssh` folder:

``` Shell
mkdir ~/.ssh
```

The `ssh-keygen` command created the SSH keys files:

To set SSH key on your local machine you need to use `ssh-keygen`:

``` Shell
ssh-keygen -t ed25519 -C "maityk007@gmail.com"
```

NOTE: The `-t` argument defines the algorithm type for the authentication key, in this case I used `ed25519` and the `-C` argument enables adding comment, in this case the user name emain for reference.

After running the `ssh-keygen` command, it will prompt for setting file name and password, which is optional. By default it will save it under the root folder.

Note: this process will generate two files:
    - `your_ssh_key` is the private key, you should not expose it
    - `your_ssh_key.pub` is the public key which will be used to set the SSH on Github

Now, the next step is to register the key on your Github account. On your account main page go to the `Settings` menu and select on the main menu `SSH and GPG keys` and click on the `New SSH key`:

-- ATTACH IMAGE --

NOTE: I set the machine nick name as the key title to easily identify the relevant key in the future.

Next step is to update the `config` file on the `~/.ssh` folder. You can edit the `config` file with `vim`:

``` Shell
vim ~/.ssh/config
```

If config file does not exist, got to `.ssh` by `cd ~/.ssh`, then command `vim config`, press `i` to activate INSERT mode

and add somewhere on the file the following code:

``` Shell 
Host *
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/your_ssh_key
```
where `your_ssh_key` is the private key file name. Then press `esc` and then `:wq` and `Enter` to save and exit from vim.


Last run the following to load the key:

``` Shell
ssh-add --apple-use-keychain ~/.ssh/your_ssh_key
```

Once the above are done, you should try to test your connection by commanding
`ssh -T git@github.com`. This attempts to ssh to Github Enterprise Cloud.

You may see a warning like below:
![TestConnectionWarning](https://github.com/KalyanbrataMaity/awesome-ds-setup/assets/49729035/3d16e10a-74e1-476c-88f7-d5d7b491cb51)

Then verify that fingerprint in the message you see matches [Github's public key fingerprint](https://docs.github.com/en/enterprise-cloud@latest/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints). If it does then type `yes`.


To not this repeat this step again and again, you can add the following ssh key entries to your `~/.ssh/known_hosts` file to avoid manually verifying Github hosts:

```Shell
github.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0SdG6UOoqKLsabgH5C9okWi0dh2l9GKJl
github.com ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEmKSENjQEezOmxkZMy7opKgwFB9nkt5YRrYMjNuG5N87uRgg6CLrbo5wAdT/y6v0mKV0U2w0WZ2YB/++Tpockg=
github.com ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCj7ndNxQowgcQnjshcLrqPEiiphnt+VTTvDP6mHBL9j1aNUkY4Ue1gvwnGLVlOhGeYrnZaMgRK6+PKCUXaDbC7qtbW8gIkhL7aGCsOr/C56SJMy/BCZfxd1nWzAOxSDPgVsmerOBYfNqltV9/hWCqBywINIR+5dIg6JTJ72pcEpEjcYgXkE2YEFXV1JHnsKgbLWNlhScqb2UmyRkQyytRLtL+38TGxkxCflmO+5Z8CSSNY7GidjMIZ7Q4zMjA2n1nGrlTDkzwDCsw+wqFPGQA179cnfGWOWRVruj16z6XyvxvjJwbz0wQZ75XK5tKSb7FNyeIEs4TT4jk+S4dhPeAUC5y+bDYirYgM4GC7uEnztnZyaVWQ7B381AK4Qdrwt51ZqExKbQpTUNn+EjqoTwvqNj4kqx5QUCI0ThS/YkOxJCXmPUWZbhjpCg56i+2aB6CmK2JGhn57K5mj0MNdBXA4/WnwH6XoPWJzK5Nyu2zB3nAZp+S5hpQs+p1vN1/wsjk=
```

------------------------------------------------------------------------------

# How to start a Github project:

OPTION 1:

1. Create a Github Repository
- Log in to your GitHub account.
- Click the "+", then "New repository".
- Name your repository and provide a brief description.
- Choose whether to make the repository public or private.
- Optionally, initialize the repository with a README, .gitignore, and license (it's easier to leave these unchecked if you're uploading an existing project).
- Click "Create repository".

2. Create a Local Repository
navigate to the root directory of your project on your local machine and run the following commands to initialize a local Git repository:

``` Shell
cd path/to/your/project
git init
```

3. Add remote Repository
Link your local repository to the GitHub repository you just created. Command the following

``` Shell
git remote add origin https://github.com/your_username/your_repository.git
```

4. Add Files to the Repository
Add all the files in the project directory to the staging area:

``` Shell
git add .
```
If you want to exclude some files from being tracked by Git, you can list them in a .gitignore file before adding the rest.

5. Commit the changes
Commit the added files with a commit message:

``` Shell
git commit -m "Initial commit"
```

6. Push to GitHub
Finally, push your local commits to the GitHub repository:

``` Shell
git push -u origin main
```

OPTION 2:

1. Create a new repository in GitHub
2. Copy the repo URL under "Clone with HTTPS"
3. open your terminal and navigate to the directory where you want to clone the repo.
4. Clone the repo by commanding: `git clone https://github.com/your_username/your_repository.git'
5. Check the clone repository `cd your_repository`
6. Verify the Cloning `ls`

Now, suppose you want to update your local Git repository with changes made in the remote repository on Github (Or any other remote repository), you'll want to use the 'git pull' command. This command fetches the latest changes from the remote repo and merges them into your local repository. Here is the steps below:

1. Navigate to the local repo `cd path/to/your/repository`

2. Make sure this is the directory containing Git repository (you should see a `.git` folder when running `ls -a`)

3. Check the remote repository: before pulling the changes, it's a good idea to see which remote servers are configured: `git remote -v`. This will list the URLs for the remote repositories (typically named origin) linked to your local repository. You can pull changes from these remote repositories.

4. Fetch the latest changes: To fetch the latest changes from the remote repository but not merge them yet, you can run:

```Shell
git fetch origin
```

5. Pull the changes: To update your local repository to the latest commit, execute the following command:
```Shell
git pull origin main
```

6. Resolve any merge conflicts: If you have any local changes that conflict with the remote changes, Git will prompt you to resolve these conflicts. You can open the conflicting files and make the necessary adjustments. After resolving conflicts, you need to add the resolved files to staging:

```Shell
git add filename
```
And then commit the resolution:
```Shell
git commit -m "Resolved merge conflicts"
```

7. Verify the Update: After pulling the changes, you can use `git log` to see the commit history and ensure that the latest commits from the remote repository are now in your local repository:

```Shell
git log
```

This process keeps your local Git repository syschronized with changes from the remote repository, allowing you to stay updated with the latest developement work.





