# Sample EC2 Service

This is an example service that shows how to access Amazon EC2 from within Rex.

## Requirements

You have to create the following files on your system (or change the source code)

* access key: /etc/rex/aws/access.key
* secret access key: /etc/rex/aws/secret_access.key
* private ssh key: /etc/rex/aws/keys/some-ssh-key.pem
* public ssh key : /etc/rex/aws/keys/some-ssh-key.pub

The default AMI id that is used in this example is from eu-west-1.

## Formulars

In the *formulars* folder you see an example formular file for Rex-JobControl.
