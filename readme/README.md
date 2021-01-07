# VAKT Interview Exam
Sample Crypto Website Project for VAKT

The final deployment was deployed into Nginx

# The CI tool implemented is Jenkins

Steps--:

Source code written into Project with js, css and img files included

2 Free Tier Linux Instances created in AWS.

The first instance has only 2 rules applied in the Security Group which are ```ssh 22 over tcp``` and ```Web 80 over tcp```

The Second instance has 3 rules applied in the Security Group which are ```ssh 22 over tcp```, ```Web 8080 over tcp``` and alternative supporting ```Jenkins port 50000 over udp```. 

Private key was generated to secure instance. On my machine terminal, I changed the ownership of the key with ```chmod og-wrx``` before being allowed to connect to the Instance.

- First Instance (nginx docker label) - with docker installed with  ```apt-get install docker.io``` and ```service docker start``` to enable docker to be used. Ran ```docker run -u root --rm -d -p 80:80 -p 31:21 -p 1022:22 -v nginx-data:/var/nginx_home -v /var/run/docker.sock:/var/run/docker.sock ubuntu-upstart``` with ```docker exec -it pensive_buck bash``` (pensive_buck bash is the name of the container).

Now that I am inside the ubuntu docker container, I installed nginx with ```apt-get install nginx``` and start the service with ```service nginx start```. By default, the port is 80 and this is also exposed externally to port 80 as well on the ec2 public address.
Nginx Web Server will now run with ec2 instance current public address on port 80


The path for the index.html is located in /usr/share/nginx/html/ where git Project will be deployed into.

- Second Instance (Jenkins will be installed), I installed Jenkins with ```apt-get install wget openjdk-8-jdk daemon nodejs -y && wget https://pkg.jenkins.io/debian-stable/binary/jenkins_2.176.1_all.deb && dpkg -i *.deb``` then started services with ```service jenkins start```
Jenkins web service will now run with ec2 instance current public address on port 8080

I have tried various protocols for the deployment, depending on what Vakt uses as the most secure as more appropriate to use as Devops Engineer.
In Jenkins, new job was created as FreeStyle Project, GitHub url was specified and the following shell script was invoked:

```node {
stage (‘Prepare environment’) {
git branch: ‘development’, url: ‘git@github.com:ayoinc/Project.git’
sh ‘npm install’
}
stage (‘Code analyse’) {
sh ‘echo “Run some lints”’
}
stage (‘Unit test’) {
sh ‘echo “Tests will back”’
}
stage (‘Build’) {
sh ‘npm run clean’
sh ‘npm run build’
}
stage (‘Deploy’) {
sh ‘ssh -P 1022 root@ec2-52-90-65-37.compute-1.amazonaws.com rm -rf /usr/share/nginx/html/’
sh ‘ssh -P 1022 root@ec2-52-90-65-37.compute-1.amazonaws.commkdir -p /usr/share/nginx/html/’
sh ‘scp -r Project -P 1022 root@ec2-52-90-65-37.compute-1.amazonaws.com/usr/share/nginx/html/temp/*’
sh ‘ssh -P 1022 root@ec2-52-90-65-37.compute-1.amazonaws.com “rm -rf /usr/share/nginx/html/ && mv /usr/share/nginx/html/temp/* /usr/share/nginx/html/”’
}
}
```

Then Build, this could be modified based the changes implemented and the need for the Client's Blockchain System.
