# Amazon Linux 2 on Docker

This docker image contain below development environment.

- rbenv
- Ruby:2.7.1
- pyenv
- Python:3.8.2
- tfenv
- Terraform:0.12.2
- Ansible:2.9.7
- aws-cli:v2

## image build

```shell
docker image build -t amzn2-awscliv2 .
```

## container run

```shell
docker container run -it amzn2-awscliv2 /bin/bash --login
```

If you want to use your `~/.aws`,`~/.ssh,` and your local volume on container, you could use `-v` option.

```shell
docker container run -it -v ~/.aws:/home/ec2-user/.aws -v ~/.ssh:/home/ec2-user/.ssh -v $(pwd):/aws amzn2-awscliv2 /bin/bash --login
```

If you want to run the container background, you need to use `-d`.

```shell
docker container run -itd --name amzn2-awscliv2 amzn2-awscliv2
docker container exec -it amzn2-awscliv2 /bin/bash --login
```

## image pull and run

You could pull this image from dockerhub.

```shell
docker container run -it revsystem/amzn2 /bin/bash --login
```
