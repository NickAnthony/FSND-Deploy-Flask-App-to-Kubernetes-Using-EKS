# AWS Deployed Flask APP

The running service is available at:
`a988355b9c840414db48480adff529c5-1553512072.us-east-2.elb.amazonaws.com`
the name is: `simple-jwt-api`

## Basic test

The following will run a basic test of the deployed API.

1. Post an email/password combo to the service
```
  export TOKEN=`curl -d '{"email":"no-reply@gmail.com","password":"veryspecialpassword"}' -H "Content-Type: application/json" -X POST a988355b9c840414db48480adff529c5-1553512072.us-east-2.elb.amazonaws.com/auth  | jq -r '.token'`
```

2. Get that email back
```
    curl --request GET 'a988355b9c840414db48480adff529c5-1553512072.us-east-2.elb.amazonaws.com/contents' -H "Authorization: Bearer ${TOKEN}" | jq
```


# Deploying a Flask API

This is the project starter repo for the fourth course in the [Udacity Full Stack Nanodegree](https://www.udacity.com/course/full-stack-web-developer-nanodegree--nd004): Server Deployment, Containerization, and Testing.

In this project you will containerize and deploy a Flask API to a Kubernetes cluster using Docker, AWS EKS, CodePipeline, and CodeBuild.

The Flask app that will be used for this project consists of a simple API with three endpoints:

- `GET '/'`: This is a simple health check, which returns the response 'Healthy'.
- `POST '/auth'`: This takes a email and password as json arguments and returns a JWT based on a custom secret.
- `GET '/contents'`: This requires a valid JWT, and returns the un-encrpyted contents of that token.

The app relies on a secret set as the environment variable `JWT_SECRET` to produce a JWT. The built-in Flask server is adequate for local development, but not production, so you will be using the production-ready [Gunicorn](https://gunicorn.org/) server when deploying the app.

## Initial setup

1. Fork this project to your Github account.
2. Locally clone your forked version to begin working on the project.

## Dependencies

- Docker Engine
    - Installation instructions for all OSes can be found [here](https://docs.docker.com/install/).
    - For Mac users, if you have no previous Docker Toolbox installation, you can install Docker Desktop for Mac. If you already have a Docker Toolbox installation, please read [this](https://docs.docker.com/docker-for-mac/docker-toolbox/) before installing.
 - AWS Account
     - You can create an AWS account by signing up [here](https://aws.amazon.com/#).

## Project Steps

Completing the project involves several steps:

1. Write a Dockerfile for a simple Flask API
2. Build and test the container locally
3. Create an EKS cluster
4. Store a secret using AWS Parameter Store
5. Create a CodePipeline pipeline triggered by GitHub checkins
6. Create a CodeBuild stage which will build, test, and deploy your code

For more detail about each of these steps, see the project lesson [here](https://classroom.udacity.com/nanodegrees/nd004/parts/1d842ebf-5b10-4749-9e5e-ef28fe98f173/modules/ac13842f-c841-4c1a-b284-b47899f4613d/lessons/becb2dac-c108-4143-8f6c-11b30413e28d/concepts/092cdb35-28f7-4145-b6e6-6278b8dd7527).

## Helpful questions that helped

### Issue: KubectlAssumeRoleCustomResource gets stuck in CREATE_IN_PROGRESS state

See https://knowledge.udacity.com/questions/298434 for adjusting the headers
of the CI config file.  You Need adjust link 158:
```
s/
headers = {'content-type': '', "content-length": len(response_body) }
/
headers = {'content-type': '', "content-length": str(len(response_body)) }
/
```

### Issue: Build Failure: error: You must be logged in to the server (the server has asked for the client to provide credentials)

See https://knowledge.udacity.com/questions/285613, for adding:
```
  – groups:
    – system:masters
    rolearn: arn:aws:iam::<YOUR_ACCOUNT_ID>:role/UdacityFlaskDeployCBKubectlRole
    username: build
```
to the aws-auth-path.yaml.  That will properly provide access to the role on
AWS.
