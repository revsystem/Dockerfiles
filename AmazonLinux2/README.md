# Amazon Linux 2 on Docker

This docker image contains below development environment.

- Ruby:2.6
- Python:3.8
- Terraform:1.1.5
- Ansible:2.9.9
- aws-cli:v2

## Docker Hub

<https://hub.docker.com/r/revsystem/amzn2>

## image build

```shell
git clone https://github.com/revsystem/Dockerfiles.git
cd Dockerfiles/AmazonLinux2
docker image build -t amzn2-awscliv2 .
```

## container run

```shell
docker container run -it amzn2-awscliv2 /bin/bash --login
```

If you want to use your `~/.aws`,`~/.ssh,` and your local volume at the container, you could use `-v` option.

```shell
docker container run -it -v ~/.aws:/home/ec2-user/.aws -v ~/.ssh:/home/ec2-user/.ssh -v $(pwd):/home/ec2-user/aws amzn2-awscliv2 /bin/bash --login
```

If you want to run the container in the background, you need to use `-d` option.

```shell
docker container run -itd --name amzn2-mycontainer amzn2-awscliv2
docker container exec -it amzn2-mycontainer /bin/bash --login
```

## image pull and run

You could pull this image from Docker Hub.

```shell
docker container run -it revsystem/amzn2 /bin/bash --login
```

If you want to run the container in the background, you need to use `-d` option.

```shell
docker container run -itd --name amzn2-mycontainer2 revsystem/amzn2
docker container exec -it amzn2-mycontainer2 /bin/bash --login
```
