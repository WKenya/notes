# Linux

General purpose tips for all universal linux commands

## Useful Commands

### Find a file

The . specifies the directory to search in.

-name tells the file to search for. It can also use wildcards.

`find . -name nginx*`

More [here][1].

### Start processes or daemons on boot (systemd)

`sudo systemctl enable {sshd}`

## Users

### Add a User

`useradd <user>`

#### Add a user to the SuperUser Group

`usermod -aG sudo <user>`

### Remove a user

`userdel <user`

## Permissions

### Change owner of a directory

`sudo chown <user>:<user> "example/path"`

[1]: https://www.linode.com/docs/tools-reference/tools/find-files-in-linux-using-the-command-line/