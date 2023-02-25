# Paperspace setup

I'm following along with the [fastai live coding session 4](https://www.youtube.com/watch?v=uKUMT6Bj4l8&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=4) to configure paperspace gradient for the fastai course.

In this post I'll write down my notes so it's easier for me to set it up again later.

The main points are the following:

* Install the fastcore library and configure the paperspace machine such that it will be stored across notebooks and servers
* Add ssh keys to paperspace machine such that one can authenticate to github.com

## What is Paperspace?

Paperspace is a company that rents out servers with products targeted at the machine learning world. They have multiple products where Gradient is the one that is interesting because it's basically jupyter notebooks in the sky, and you get to borrow and rent GPUs.

Paperspace has a free tier, but I had to sign up for the $8 a month Pro tier before I was able to open the terminal on the server. And for getting set up we would like to make some configurations in the terminal.

Note that they do have a [referral program](https://www.paperspace.com/referral-program) where you get $15 credit for each person you get to signup.

## How does a Paperspace machine work?

Each machine you run is ephemeral, meaning everything you do there will be lost. However, there is a `/storage` folder on each machine which is stored persistently. Note that if you store more than your allocated amount inside of this folder, they will charge you $/GB.

## Setting up

### Create a notebook

Create a notebook and choose the "Peperspace + Fast.AI" runtime. Choose whichever GPU you'd like.

Remove the contents of the Workspace URL (in advanced options).

Auto-Shutdown X hours means that the machine will automatically be terminated after X hours (even if you are currently active on it).

### Terminal configuration

What we'd like to achieve is to set up the environment so it is ready to run fastai stuff and that you won't have to re-do any of the steps later.

#### Install fastcore library

Open the terminal.

On the current paperspace server you are on, `/notebooks` is a persistent storage for the specific server while `/storage` is a persistent storage across servers.

Install fastcore python library.

```sh
pip install -U --user fastcore
```

This will be installed inside of the `.local` directory in your home directory. The next step is to move this to persistant storage so you don't have to reinstall it in your next session.

Move to the home dir.

```sh
cd
```

Create a configuration folder in your persistant storage.

```sh
mkdir /storage/cfg
```

Move your .local dir to your configuration dir.

```sh
mv .local /storage/cfg
```

Create a symbolic link to the persistant version of .local.

```sh
ln -s /storage/cfg/.local/
```

From now on, when you make changes to `.local`, they will be stored for the future.


##### A note on installing packages with pip alongside conda

Is this safe? Will not conda and pip conflict?

Officialy it is unsafe. However there are so many people who has done it and it seems like no one has had any problems with combining pip and conda that you don't really need to worry about it. In general, conda is required for stuff that needs GPU because it installs dependencies outside of python that pip does not handle, and further it will handle the version maintaining of the dependencies in the future. Otherwise using pip is fine.


#### Make files in `/storage` available to edit from jupyter

While you are in the `notebooks` dir, run the following command

```sh
ln -s /storage/
```

This command creates a symbolic link to the `storage` folder inside of your `notebooks` folder which is accessible from the jupyter notebook in paperspace.

If you have opened jupyter lab in paperspace, you should now see a storage folder there (if not, try to click the refresh button).

Now we can create a little script to automate the process of creating a symbolic link to the .local folder.

Click + and "Text file" and insert the following:

```sh
#!/usr/bin/env bash

# open home folder
cd

# remove any existing .local folder
rm -rf .local

# symlink to the .local folder in storage
ln -s /storage/cfg/.local/

```

Now, click File > Save Text (or Ctrl + S) to save the file.

Then rename the file by right-clicking it and click rename (or left click and F2): "prerun.sh"

`pre-run.sh` is a special file in paperspace which is run before a jupyter notebook instance is started.

For the machine to be able to run this file, we will have to give it permission to execute it, so head back to the terminal and run the following commands.

```sh
cd
```

```sh
chmod 744 pre-run.sh
```

This gives the user read, write and execute permission (the 7), while the group gets read (the first 4) and others also get read (the second 4).

How to know which numbers to pick? Add together numbers from the following:

* 1: execute
* 2: read
* 4: write

For example read and execute is 3 (2 + 1).

#### SSH keys (for github authorization)

```sh
cd /storage
```

```sh
mkdir .ssh
```

Upload your ssh keys to the /storage.ssh folder by clicking "Upload Files" (arrow to the right of the blue + button), find your public and private keys and click "Open" or whatever is appropriate for your system.

Fix permissions for the ssh files:

```sh
chmod 700 .ssh
```

```sh
chmod 600 .ssh/id_rsa
```

```sh
chmod 644 .ssh/id_rsa.pub
```

Now create a symlink to the `/storage/.ssh` folder from your home directory.

```sh
cd
```

```sh
ln -s /storage/.ssh
```

Also add this symlink command to the `/storage.pre-run.sh` script so you don't have to do it again in the future.

Now you can test authentication with the following command (remember to add the public key to your github.com account in Settings > SSH and GPG keys > New SSH key if it's not already there).

```sh
ssh git@github.com
```

If you're denied, you can get more info by adding `-v`

```sh
ssh -v git@github.com
```

The more v's you add, the more verbose the output will be.

Note that you if you are security minded, you'd might want to create ssh keys on the server with `ssh-keygen` instead of uploading the ones on your computer. The reason why Jeremy does this, is that it creates less friction and overhead as he authenticates from a lot of differents servers.


## The end

Hope this helped!

I'd like to finish the post with a quote from Jeremy with some wisdom that I'd like to aspire to:

"I like to learn as few things as possible because I'm lazy, and the way to get away with being lazy and learning as few things as possible is to learn things which are really versatile and powerful so that they can do a lot of things."

For example he has not spent too much time on bash scripting, and rather use python for most scripting because python can be used for more things than bash scripting.

Some other examples he has for this:

* In paperspace gradient, open the notebooks in jupyter instead of using their proprietary GUI as whatever you learn in jupyter can be used also outside of paperspace
* Read the documentation to find out how things work instead of spending a lot of time trying to figure it out yourself

I had some experience with this at work where we've set up a server with the purpose of doing ETL jobs. With Python it has been not that difficult to get the server to:

* do simple db to db table replication jobs (pandas)
* run dbt for modeling data (with terminal commands through the os library)
* expose the functionality above through an API (FastAPI) to be called from an orchestration tool

It is certainly possible to get the same functionality with other languages, but I suspect it would often require more time to get it to work.

I recommend watching the fastai live coding sessions because Jeremy shares a lot of knowledge tools and !
