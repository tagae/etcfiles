etcfiles
========

Use version control to manage your /etc files.


Setup
-----

1. On the admin client, gain password-less root access to your server:

        ssh-copy-id -i ~/.ssh/id_rsa.pub root@server

1. On the server, edit /etc/ssh/sshd_config and set

        PermitRootLogin without-password

1. On the server do

        cd /etc
        git init
        git submodule add git://github.com/tagae/etcfiles.git .bin
        git submodule update --recursive --init
        git submodule foreach --recursive 'git checkout master'
        git commit --message "Started versioning /etc."

   To enable auto-merging of edits to the master branch, do

        cd .git/hooks
        ln -s ../../.bin/etcfiles-post-receive-hook post-receive

1. On the admin client do

        cd local-etc
        git init
        git remote add server ssh://root@server.org/etc
        git fetch --all
        git checkout -t server/edits

   The last step won't work until you import at least one file using
   `etcfiles-import`.

Workflow
--------

### Tracking new configuration files

On the server, do

    /etc/.bin/etcfiles-import <file>

Before importing a file on the server, make sure your VISUAL
environment variable is configured properly, so that you can edit the
commit message.

### Editing configuration files

On the admin client, do

    git pull

commit your edits, and finish with

    git push

to send the changes to the server. A remote hook will merge those
changes with the master branch, so that they are effective
immediately.

### Updating etcfiles installation

_To be explained._


Cleanup
-------

To remove version tracking from the server, do

    cd /etc
    rm -rf .git .gitmodules README.md .bin
