---
layout: post
title: Configure a Synology NAS as a git server
date: 2015-09-02 13:45:47
category: ops
tags: [ops, NAS, synology, git]
---
[Github](https://github.com) is great but for private work, e.g. [Coursera](https://www.coursera.org/)
courses or [Project Euler](https://projecteuler.net/), it'd be good to have your own git server. If you have a
[Synology](https://www.synology.com) [NAS](https://en.wikipedia.org/wiki/Network-attached_storage),
here's how to run a git server on it.

These instructions work on Synology disk stations running [DSM 5.2](https://www.synology.com/en-us/dsm/5.2).

## Set up a user for git

* Logged in as `admin` on the Diskstation web console, open *Control Panel*
* Click on the *Advanced* tab, enable the *user home* service, and *Apply*
* Click on the *User* tab, *Create* a new user with a password, e.g. `gituser`
* In *Control Panel*, scroll to the bottom of the left pane and click on *Terminal & SNMP*
* Under the *Terminal* tab, enable the *SSH* service, and *Apply*
* Close *Control Panel*, open *Package Center* and install *Git Server*
* Once the *Git Server* is installed and running, close *Package Center*
* Click on *Main Menu* (top left) and open *Git Server*
* Enable git server access to your new user, e.g. `gituser`

The new user created has a login shell set to `git-shell` which limits the user to git commands only.
In my case, I'd like to ssh on to the Diskstation with the *normal* `ash` shell (a lightweight version of `bash`),
so I can create new git repos without ssh'ing in as `root` (which is given in other how-to's).
Ssh in as `root` and edit the `/etc/passwd` entry for your new user back to an `ash` shell login:

* SSH in as root: `ssh root@diskstation.local`
* Get to the directory: `cd /etc`
* Back up the orignal file: `cp passwd passwd.backup`
* Edit the file e.g. `vi passwd`
* Find the new user entry, e.g. `/gituser`
* Find the start of the git-shell entry: `/:/var/packages`
* Delete to the end of line: `d$`
* Add the ash entry: `a:/bin/ash`, hit ESC key
* Save the file: `:wq`
* Exit out of Diskstation: `exit`
* SSH in as your new user: `ssh gituser@diskstation.local`
* Create a new directory for your git projects: `mkdir git`

## Create a new bare git repo on the server

Once a bare git repo is initialised, you can start using git:

* In the SSH session above (`ssh gituser@diskstation.local`)
* Go into the directory: `cd git`
* Create the file: `createProject.sh` with the contents from this gist:

{% gist oldgit/7c489531d79a19c578c2 createProject.sh %}

* Make it executable: `chmod +x createProject.sh`
* Make a new git repo, say *test* with: `./createProject.sh test`
* The script will warn you if a repo with the same name exists & list all git repos, so you can choose a unique name
* Once created, exit out of Diskstation: `exit`

## Clone your new bare git repo

Back on your client machine, you can now clone the new repo:

{% highlight text %}
$ git clone gituser@diskstation.local:~/git/test.git
Cloning into 'test'...
gituser@diskstation.local's password:
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
$ cd test
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
{% endhighlight %}

You can now start using your new git repo and push & pull to your own git server.

**Note:** You'll need to generate SSH keys and place your public key on the git server
to avoid continual login prompts.
See [Matt Heimer's great post](http://blog.osdev.org/git/2014/02/13/using-git-on-a-synology-nas.html) for this.
**However**, I didn't need to edit `/etc/ssh/sshd_config` - I did ensure that
the `.ssh` directory and `authorized_keys` had only user `'rw'` permissions.
Also he thoroughly explains the *WebDAV* way to use git on your NAS.

## Creating new git repos straight from your client

As you have a decent ssh login as `gituser` to the NAS, it's easy to remotely call: `createProject.sh`.
So if you just wanted to see a list of git repos on the NAS from your client:

{% highlight text %}
$ ssh gituser@diskstation.local "cd git; ./createProject.sh test"
test.git already exists!
Please use a name not in this list:
euler.git
test.git
{% endhighlight %}

So let's create a `test2` git repo on the NAS:

{% highlight text %}
$ ssh gituser@diskstation.local "cd git; ./createProject.sh test2"
Initialized empty shared Git repository in /volume1/homes/gituser/git/test2.git/
euler.git
test.git
test2.git
Created git project: test2.git
{% endhighlight %}

If you are doing this often, add a function in your `bash` or `zsh` rc file:

{% highlight bash %}
function createNASgit() {
  if [ $# -eq 1 ]; then
    ssh gituser@diskstation.local "cd git; ./createProject.sh $1"
  fi
}
{% endhighlight %}

Now you can just run: `createNASgit test`
