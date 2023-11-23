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
$ cat <EOF >> ~/.ssh/config
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

## Step 4: clone or update your repos
