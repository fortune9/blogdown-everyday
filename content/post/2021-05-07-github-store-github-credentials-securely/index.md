---
title: '[github] store github credentials securely'
author: Zhenguo Zhang
date: '2021-05-07'
slug: github-store-github-credentials-securely
categories:
  - Computing
  - Tips
tags:
  - Programming
  - github
description: ''
featured_image: ''
images: []
comment: yes
---

As a developer, I tried to do things efficiently. One thing that I
want to fix is avoid input the password (or personal access token)
when push to github. Actually, this is not an easy problem because
I did not find a comprehensive webpage to provide step by step
guide on how to achieve this goal. After researching this issue
for one day, finally I could push to github without inputting any
password, and I want to share how I achieved this to help anyone
who may struggle for this.

I will talk about the settings for Windows and Ubuntu.

## Windows

For Windows OS, the setting is relatively easy.

1. Install the git program from [https://git-scm.com](https://git-scm.com/download/win).

2. Then create a file at "c:\Users\{username}\.gitconfig".
  And edit the file in an editor. An simple example is below:

```
[user]
        email = myid@gmail.com
        name = First Last

[credential "https://github.com"]
        username = mygithubid
# the following can be omitted, as it is set in
# D:\Tools\Git\etc\gitconfig
[credential]
        helper = manager-core
[credential "https://dev.azure.com"]
        useHttpPath = true
```

  The first section set `user.name` and `user.email` variable for
  git, which will be shared by all repositories. The next section
  [credential "https://github.com"] means that all github
  repositories will use the same username 'mygithubid'. One can
  add option 'useHttpPath =  true' in this section to force each
  repo uses its own credentials.
  
  Also, one can add sections like 
  '[credential "https://github.com/mygithubid/repo1.git"]'
  to allow repo-specific settings.
  
  Here the key is the last two sections: '[credential]'
  and '[credential "https://dev.azure.com"]'. The former
  tells git to use `manager-core` to store credentials
  and the latter tells git to match the accounts in
  "https://dev.azure.com" with path component.
  
  Here the credential helper `manager-core` internally trigger
  the program 'git-credential-manager-core' and handle the
  credentials. It is bundled with the Windows git program, so
  no need to install it separately. One can find the information
  on the helper at https://github.com/microsoft/Git-Credential-Manager-Core.
  
3. Run `git push` or other commands needing credentials will
  trigger the helper program. When prompted, one can input
  pass code, and after that, no more prompts will be asked
  in future as the code is already stored safely.
  
## Ubuntu

Based on the Git-Credential-Manager-Core page, it seems that
Linux can also use it for handling credentials. However,
I couldn't succeed after trying both `secretservice` and `gpg`
for the git config option 'credential.credentialStore', when
setting it in a PuTTY terminal.

Therefore, I tried another method, [pass-git-helper](https://github.com/languitar/pass-git-helper).

This helper program utilizes the `gpg` and `pass` to handle
credentials. The key idea is that it maps the github urls to
the entries to the password store, using a config file
`~/.config/pass-git-helper/git-pass-mapping.ini`.

Let's start.

1. Install the program `pass-git-helper` by following the 
  instruction at https://github.com/languitar/pass-git-helper.
  The simple way is to download the tar.gz file, uncompress
  it and then run `sudo pip install .` in the folder.
  
2. Generate a gpg key pair, which will be used to encrypt
  the password store.

```bash
gpg --generate-key
# follow the instruction to finish the key generation
``

**For gpg works properly, one may also need do the following
setting**:

* put `export GPG_TTY=$(tty)` to file '~/.bashrc'.
* in '~/.gnupg/gpg-agent.conf', specifying the pin-entry
  program, such as `pinentry-program /usr/bin/pinentry-tty`.
  One can replace this with others, choice of which can be
  found by running `ls /usr/bin/ | grep pinentry`.

Remember the key uid, say "myid@gmail.com", which will be
used in next step.

3. Initialize password store with the new key.

```
pass init "myid@gmail.com"
```

This will creates a folder '~/.password-store' if not exists.
If it exits, the above command will re-encrypt the existing
passwords in this folder. If one wants a new folder for passords,
use the option '-p newfolder' in the above command.

Also, one can sepcify multiple gpg-ids, so each of them can be
used to encrypt and decrypt the passwords. See the file
'~/.password-store/.gpg-id' for used gpg-ids.

4. Insert new password to the store

```bash
pass insert dev/github
```
The above command creates file 
'~/.password-store/dev/github.gpg', containing the password.
and remember the password id 'dev/github', which will be used
in the mapping file `~/.config/pass-git-helper/git-pass-mapping.ini`. One can
use any password id he likes.

One can access the password with the following command to
make sure the command is correctly setup.

```bash
pass dev/github
```

5. Setup the pass-git-helper mapping file.

Open the file '~/.config/pass-git-helper/git-pass-mapping.ini',
and add the following into it:

```
[github.com/*]
target=dev/github
```

This file tells all github.com repositories will use the
password stored in 'dev/github'.

If one wants a certain github account or repository use
a specific password, it can replace 'github.com/*' with
corresponding URL patterns, such as 'github.com/githubid/*'.

6. Setup the .gitconfig file to use the helper 'pass-git-helper'

One can refer to the section [Windows](#windows) for how to
setup '~/.gitconfig' file. Other than that, one can run
the following command to add the helper:

```bash
git config --global credential.helper '!pass-git-helper $@'
# or edit '~/.gitconfig' to add this option
```

7. Finally, test it.

Find a github repository, and run `git push` to test it.
It should not prompt any password.

Congratulations!!!

## References

1. gpg and pass usage: https://medium.com/@davidpiegza/using-pass-in-a-team-1aa7adf36592

2. git-credential-manager-core setup: https://github.com/microsoft/Git-Credential-Manager-Core/blob/master/docs/linuxcredstores.md

3. git credential configuration: https://git-scm.com/docs/gitcredentials

4. pass-git-helper: https://github.com/languitar/pass-git-helper