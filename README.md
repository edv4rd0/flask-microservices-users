# flask-microservices-users

This is the results of me following the tutorial at: https://testdriven.io/

However, I had to make a change and remove a volume from the docker-compose users-service container as it overwrote the /usr/src/app directory that everything was copied into in the Dockerfile.

## Docker Machine Environment

`docker-machine` is used to handle environments for `docker-compose` to push to. 

## Local Environment

For local development and testing we will use a virtualbox based host. Use the following commands to set this up.

```
$ docker-machine create -d virtualbox dev
$ eval "$(docker-machine env dev)"
```

To get the IP address of the host machine, use `docker-machine ip dev`.

Helpful explanation: https://stackoverflow.com/questions/40038572/eval-docker-machine-env-default/40040077#40040077

## AWS Environemnt

For staging, we can push to AWS and run there. Create an AWS CLI user with the following permission profile:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecs:CreateCluster",
                "ecs:DeregisterContainerInstance",
                "ecs:DiscoverPollEndpoint",
                "ecs:Poll",
                "ecs:RegisterContainerInstance",
                "ecs:StartTelemetrySession",
                "ecs:Submit*",
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

Save the credentials and add them to `~/.aws/credentials`.

To create the environment use the following commands:

```
docker-machine create --driver amazonec2 --amazonec2-region "ap-southeast-2" --amazonec2-vpc-id=vpc-XXXXXX --amazonec2-private-address-only aws-sandbox
docker-machine env aws-sandbox
eval $(docker-machine env aws-sandbox)
```

To get the IP address of the host machine, use `docker-machine ip aws-sandbox`.

If deploying to a public subnet, you can rmeove `--amazonec2-private-address-only`.

Now, when running `docker compose up -d` it will deploy to AWS. Check the EC2 Console and you will see a new EC2 instance running there.

## Building and Running

To build and run: `docker-compose up -d --build`

Build: `docker-compose build`

Run: `docker-compose up -d`

### Create Database

First, create the databases and then seed with base user data.

```
docker-compose run users-service python manage.py recreate_db
docker-compose run users-service python manage.py seed_db
```

## Testing


To run all tests use the following command after running `docker-compose up -d`:

```
docker-compose run users-service python manage.py test
```


