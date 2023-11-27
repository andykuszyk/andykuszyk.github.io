# Using multiple SSH keys for git authentication
If you're using more than one account with a git forge like GitHub, you might want to use a separate SSH key for each account on the same machine.

This allows you to ensure you're not sharing sensitive credentials between different accounts, but still gives you the flexibility of interacting with your git repos as normal from the same machine.

Using more than one SSH key for git operations on the same machine is quite easy, but requires a small amount of configuration. In this blog post I'll outline the steps to do so. You can always test this out yourself by using two different SSH keys for the same account, and then following the rest of the steps.

## Step 1: create SSH keys for each account
First, you need an SSH key for each account you want to access. Let's assume we've got two accounts we want to use called `jim` and `spock`:

```bash
$ cd ~/.ssh

$ ssh-keygen -f jim
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in jim
Your public key has been saved in jim.pub

$ ssh-keygen -f spock
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in spock
Your public key has been saved in spock.pub
```

After running these commands, you should have four new files in your `~/.ssh` directory:

```bash
$ ls ~/.ssh
jim jim.pub spock spock.pub
```

## Step 2: configure your SSH agent to use your keys
Next, you need to configure your SSH agent to use each of your keys depending on which hostname you use for the remote git server:

```bash
$ cat <<EOF >> ~/.ssh/config
Host jim
	HostName github.com
	User git
	IdentityFile ~/.ssh/jim

Host spock
	HostName github.com
	User git
	IdentityFile ~/.ssh/spock
EOF
```

This tells the SSH agent that when you `git clone git@jim:jim/somerepo` it should:

- Actually use the real hostname `github.com`, rather than `jim` which you provided.
- Use the SSH key located at `~/.ssh/jim` to authenticate with the git server.

## Step 3: ensure your SSH keys are loaded
Next, you need to make sure that your SSH agent knows about both keys. You can do this with the `ssh-add` program:

```bash
$ ssh-add ~/.ssh/jim
$ ssh-add ~/.ssh/spock
```

> 💡 If you're adding the SSH keys on a Mac, you might want to use the `-K` flag: `ssh-add -K ~/.ssh/jim`.

## Step 4: clone or update your repos
Finally, you can either clone repos using your custom hostnames, or update your existing repos.

In order to clone a repo and use the SSH keys you've configured, you need to use the same hostname that you specified in your `~/.ssh/config`. For example, to use the `jim` identity, you would clone a repo like this:

```bash
$ git clone git@jim:jim/somerepo
```

Just to clarify, if we breakdown the repo address, each component can be described as follows:
- `git@`: use SSH authentication.
- `jim:`: the hostname we aliased in our `~/.ssh/config`. This just tells the SSH agent which key to use, but the real hostname of `github.com` will actually be used since you specified it in the `HostName` directive.
- `jim/`: this is the user account the repo is stored in.
- `somerepo`: the name of your repo.

If you want to do this for an existing repo that you've already cloned, you just need to edit the repo settings in the `.git/config` file.

You might have a config block like this in the file:

```ini
[remote "origin"]
    url = git@github.com:jim/somerepo
```

If this is the case, you just need to change the address of the remote repo to match the one you would use on a fresh clone:

```ini
[remote "origin"]
    url = git@jim:jim/somerepo
```

Whilst you're editing your per-repo config, you might also want to override your default user name and e-mail address to match the SSH key you're using:

```ini
[user]
	email = jim@starfleet.com
	name = jimkirk
```
