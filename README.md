# jenkins-to-salesforce

Simple jenkins image with Force.com Migrations tool.

Please report any issues or comments here:
https://github.com/anyei/jenkins-to-salesforce/issues

## Features
Tools needed to make deployments to salesforce.com included in this image:
* Force.com Migrations Tool ([official resources]( https://developer.salesforce.com/page/Force.com_Migration_Tool ))
* https://ant.apache.org/bindownload.cgi

#### plugins installed
Besides the plugins which comes with the jenkins, the following are some additional plugins included in this image:
git
git-client
promoted-builds
scm-api
token-macro
mailer
parameterized-trigger

## Usage

This is the basic command to start jenkins:
```sh
$ docker run -p 8080:8080 anyei/jenkins-to-salesforce
```
This will store all jenkins data and configuration in /var/jenkins_home. You can make that a persistent volume:

```sh
$ docker run --name jenkins-sfdc -p 8080:8080 -v /var/jenkins_home anyei/jenkins-to-salesforce
```

You can bind volume from host and container: 
The jenkins user in container (jenkins user - uid 102, same as base jenkins image) must have access to /usr/back/jenkins:

```sh
$ docker run -p 8080:8080 -v /usr/back/jenkins:/var/jenkins_home anyei/jenkins-to-salesforce
```

You can do it all in one command too : 
```sh
$ docker run --name jenkins-sfdc -p 8080:8080 -p 50000:50000 -v /usr/back/jenkins:/var/jenkins_home -d anyei/jenkins-to-salesforce
```

Please refer to jenkins official usage for a more complete explanation on the rest of jenkins matter: https://hub.docker.com/_/jenkins/


### Public key authentication with git
If you have a jenkins job setup with git plugin, the best way to authenticate against the server where the target repository lives is with a public key. Jenkins runs as jenkins user within the container, therefore you'll need jenkin's user public key.

Get inside the running container (we are assuming the containers name is jenkins-sfdc):
```sh
$ docker exec -it jenkins-sfdc /bin/bash 
```
Then generate the rsa key pair :
```sh
$ ssh-keygen -t rsa 
```
Follow the instructions, but leaving everything in blank and just hitting enter key will be enough for the public and private key to get generated. Assuming you did so, the public key should be located in the following file /var/jenkins_home/.ssh/id_rsa.pub. 

You can use the content of the file to setup ssh keys to gain access to git repositories, github is a very good example: Go to your github account, then go to settings and then ssh keys, when you add a new ssh key copy the content of the id_rsa.pub file and paste it in the new entry's key field. Also you can use the content with authorized_keys files by appending the content of id_rsa.pub to any authorized_keys file in the target repository server.

Now you go to your jenkins job in the web interface, click on configure button and then go to Source Code Management section, assuming you have the git option selected, click on the add button in the credentials field little bit down bellow the git repo url, choose SSH Username with private key as the kind and select the option from the jenkins master (~/.ssh) and if you set any passphrase put it in the next field as well, then click add button and then the save button at the end. 


### Documentation
You can find more about jenkins image in the official [docker repository](https://hub.docker.com/_/jenkins/).

You can find good example how to setup jenkins to deploy to saleforce in [Github wiki page](https://github.com/anyei/jenkins-to-salesforce/wiki)

### Issues
Let's keep this in [Github](https://github.com/anyei/jenkins-to-salesforce/issues).

### Contributing

You are invited to contribute, let's keep in touch in [Github](https://github.com/anyei/jenkins-to-salesforce)


