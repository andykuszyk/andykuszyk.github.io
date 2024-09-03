

# Editing encrypted files in Emacs

I routinely store note files (normally using `org-mode`) in Git repos, which I commit automatically using [git-auto-commit-mode](https://github.com/ryuslash/git-auto-commit-mode).

This works really well, but sometimes I'm working on some notes that I want to be ultra-safe in the event that my Git repo is maliciously accessed. On these occasions, I make use of Emacs' [transparent support for editing GPG-encrypted files](https://www.gnu.org/software/emacs/manual/html_node/epa/Encrypting_002fdecrypting-gpg-files.html). Emacs does a fine job of transparently decrypting files when I open them, and encrypting them when I save them. However, I need to make sure that the encryption keys I use for the files are portable across machines, otherwise I might end up with files I can never open again! 😅

In this post, I briefly describe how I make keys portable, as well as how I use Emacs to edit encrypted files.


## Generating, exporting, and importing a GPG key

Before using a key to encrypt your notes, you need to generate a key, and make sure you can use it between machines.


### Step 1: generating a new key-pair

First of all, generate a new key with:

    gpg --full-generate-key

Once you've generated a key, you should be able to see it with:

    gpg --list-keys

You should see some output like this:

    pub   ed25519 2024-01-01 [SC]
          3453298457893DFHJKEHFGKJHFGKJSEH34Y78365
    uid           [ultimate] James Kirk (Self-destruct key) <jkirk@starfleet.com>
    sub   cv25519 2024-01-01 [E]

Make a note of the key's ID, which is `3453298457893DFHJKEHFGKJHFGKJSEH34Y78365` in this example.


### Step 2: exporting the keys

Now, you can export it as plain-text with:

    gpg --armor --export-key 3453298457893DFHJKEHFGKJHFGKJSEH34Y78365 > public.key
    gpg --armor --export-secret-key 3453298457893DFHJKEHFGKJHFGKJSEH34Y78365 > private.key

Keep these files safe, and take care transmitting them to your other machines! I store them in a password manager.


### Step 3: importing they keys elsewhere

These keys can be imported with:

    gpg --import public.key
    gpg --import private.key

And, finally, your imported key can be trusted with:

    gpg --edit-key 3453298457893DFHJKEHFGKJHFGKJSEH34Y78365 trust quit

> 💡 Choose option 5, ultimate trust.

Now you're all set with an encryption key you can distribute between your machines! 🔒


## Configuring Emacs to work well with encrypted files

If you want to automatically encrypt files with a `*.gpg` extension when you save them, you might also find these Emacs settings useful.

First, allow passphrases to be entered directly in Emacs from the minibuffer:

    (setq epg-pinentry-mode 'loopback)

Next, disable the dialog you're normally presented with to select an encryption key:

    (setq epa-file-select-keys nil)

Finally, in order for [`epa-file-select-keys`](https://www.gnu.org/software/emacs/manual/html_node/epa/Encrypting_002fdecrypting-gpg-files.html#index-epa_002dfile_002dselect_002dkeys-1) to take effect, set [`epa-file-encrypt-to`](https://www.gnu.org/software/emacs/manual/html_node/epa/Encrypting_002fdecrypting-gpg-files.html#index-epa_002dfile_002dencrypt_002dto) as a [directory-local variable](https://www.gnu.org/software/emacs/manual/html_node/emacs/Directory-Variables.html):

    ((nil . ((epa-file-encrypt-to . "jkirk@starfleet.com"))))

If, like me, you're also making use of `git-auto-commit-mode`, your `.dir-locals.el` might look like this:

    ((nil . ((eval git-auto-commit-mode 1)
             (epa-file-encrypt-to . "jkirk@starfleet.com"))))


## Summary

So there you have it! If you want to make use of Emacs' built-in GPG encryption capabilities to automatically encrypt `*.gpg` files when they're saved, you need to:

1.  Generate, store, and distribute a GPG key that you can use everywhere you want to encrypt/decrypt your notes.
2.  Set the `epg-pinentry-mode` and `epa-file-select-keys` variables globally.
3.  Set the `epa-file-encrypt-to` on a per-file, or per-directory basis.
4.  `C-x C-s` and profit!

