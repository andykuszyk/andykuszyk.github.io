

# Entering GPG passphrases with Emacs when signing commits with GPG

I recently set-up commit signing with Git for the first time since I've been using Magit in Emacs as my daily-driver Git client. It turns out, if you use a passphrase for your GPG keys, it's a little tricky to setup Emacs as the pinentry client for entering passphrases. I've written down what I did to get this working in the hopes that it will save my future self (and perhaps you!) the effort of finding out how to do it.


## Step -1: setting up your GPG key

In order to sign Git commits with GPG, you'll need a GPG key:

    gpg --full-generate-key

Be sure to set a passphrase! 🔒

Next, you'll probably need to export the public key and let your Git forge (e.g. GitHub) know about it. List your keys:

    $ gpg --list-keys
    [keyboxd]
    ---------
    pub   ed25519 2380-10-01 [SC]
          53467HJGRGHJFYH438756YUTR784365785YUIHYJ
    uid           [ultimate] Jim Kirk <jim.kirkg@starfleet.com>
    sub   cv25519 2380-10-01 [E]

And then use the key ID to export it in plain-text:

    $ gpg --export --armor 53467HJGRGHJFYH438756YUTR784365785YUIHYJ
    -----BEGIN PGP PUBLIC KEY BLOCK-----
    ...
    -----END PGP PUBLIC KEY BLOCK-----


## Step 0: setting up Git

Next, you'll want to enable commit signing automatically:

    git config --global commit.gpgsign true

And tell Git which key to use:

    git config --global user.signingkey 53467HJGRGHJFYH438756YUTR784365785YUIHYJ

Now, if you try to commit from a terminal you should be prompted for your passphrase. If you're not, you're probably missing this environment variable in your shell's `.rc` file:

    export GPG_TTY=$(tty)


## Step 1: Emacs as a `pinentry` client

Alright, now that you've got commit signing setup, what we actually need to do is configure Emacs to a be a `pinentry` client.

First, make sure you've got it installed. For Mac OS, run:

    brew install pinentry

Next, in Emacs, install the client package:

    (use-package pinentry :ensure t)
    (pinentry-start)

Finally, configure GPG to use Emacs. Put this in you `gpg-agent.conf` file, which for me is at `~/.gnupg/gpg-agent.conf`:

    allow-emacs-pinentry

Enjoy passphrase-protected commit signing! 🎊

