## 🔑 GitHub SSH manager

> A lightweight, zero dependency utility for managing SSH keys for multiple GitHub repositories on a single machine. It's essentially a simplified version of the workflow described here: https://docs.github.com/developers/overview/managing-deploy-keys.

<p align="center">
  <img src="https://i.imgur.com/2lnzTEb.png">
</p>

### Installation
Either clone the repository as normally or copy the file contents and paste it in a new script on your machine (make sure to mark it as executable). You may also download the latest version using `wget`, but as always, make sure to download it separately and read it thoroughly before running it the first time.

```console
$ sudo wget -O /usr/local/bin/gh-ssh "https://raw.githubusercontent.com/Kattjakt/github-ssh-manager/main/gh-ssh"
$ sudo chmod +x /usr/local/bin/gh-ssh
$ gh-ssh # woo!
```

### Example usage
Presume we have a GitHub user `foo` with a private GitHub repository called `application`. We have a remote machine that must be able to access the repository in order to continuously pull the latest version, so we need to set up SSH keys and create a new Deploy Key.

Create a new labeled SSH key pair in `~/.ssh/github/foo/application/` and add it to our `ssh-agent`
```console
$ gh-ssh init foo/application
```

We can now check the status of our SSH keys by running the following command. 
```console
$ gh-ssh ls
REPOSITORY        ACTIVE  PRIVATE KEY  PUBLIC KEY      LAST UPDATED              
foo/application   No      id_ed25519   id_ed25519.pub  Sat Dec 26 03:27:04 2020
```
Note that the "repositories" listed here isn't actually stored anywhere; they get inherited from the folder structure under `~/.ssh/github/`. The column "active" indicates whether or not the repository is listed in `~/.ssh/config`. As we see, our repository doesn't exists in there, so lets enable it!

```console
$ gh-ssh enable foo/application
```

If you try running the above command yourself (and it succeeds) you'll see that it also outputs instructions for creating a Deploy Key in the GitHub web interface. For the sake of easability we also print the public key here for quick copy-paste, but of course you can just run `cat ~/.ssh/github/<user>/<repo>/id_ed25519.pub` at any time to retrieve your public key. You can read more about the Deploy Key process [here](https://docs.github.com/en/free-pro-team@latest/developers/overview/managing-deploy-keys#setup-2).

When you've saved the Deploy Key you can make sure everything works by running
```console
$ gh-ssh test foo/application
```

If it succeeds, great! If it didn't, it should tell you what went wrong and how to fix it.

So, to summarize: we've created an SSH key pair, added them to our `ssh-agent`, and created a new entry in `~/.ssh/config`. The only thing that remains is to clone the repository itself. We can get the git origin URL by running the `origin` command:

```console
$ gh-ssh origin foo/application
git@foo_application:foo/application.git

$ git clone $(gh-ssh origin foo/application)
Cloning into 'application'...
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
Receiving objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0

$ ls
application
```

And that's it! Run `gh-ssh` to print all available commands!

