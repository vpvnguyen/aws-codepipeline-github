# aws-codepipeline-github

Build a CI/CD AWS CodePipeline

## Github

- Create a bucket in S3

- Log into AWS and go to CodePipeline
- Click on Create Pipeline
- Choose Source Provider - Github
- Connect to Github then choose repository and branch
- Create Project and give it a name
- Choose OS
- Choose aws/codebuild/standard:1.0
- Continue to CodePipeline
- Choose Deploy provider - Amazon S3
- Choose region
- Create bucket - set bucket to the app set in S3
- Extract file bfore deploy
- Next and Create pipeline

  > By default the pipeline will try to deploy. Need to setup a yaml file that will be used to work with AWS

- Go into target repo and setup a `buildspec.yml` file with the following example:

```yml
version: 0.2

phases:
  install:
    commands:
      - echo Installing Node 12...
      - curl -sL https://deb.nodesource.com/setup_12.x | bash -
      - apt install -y nodejs
      - echo Installing Yarn...
      - curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
      - echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
      - apt install --no-install-recommends yarn
  pre_build:
    commands:
      - echo Installing dependencies...
      - yarn
  build:
    commands:
      - echo Testing...
      - yarn test
      - echo Building...
      - yarn build
artifacts:
  files:
    - "**/*"
  discard-paths: no
  base-directory: dist
```

- commit the `buildspec.yml` file
- AWS Codepipeline will now start to build and compile
- Go back to S3 bucket and verify that the application has been deployed
- Go to the Properties tab and select Use this bucket to hose this website
- Set the index of the document
- Got to Permissions tab and ensure that you have public access
- Under Bucket Policy, create a policy for the bucket

```
{
    "Version": "2012-10-17",
    "Id": "Policy1555582784303",
    "Statement": [
        {
            "Sid": "Stmt1555582783256",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3.GetObject",
            "Resource": "arn:aws:s3:::NAME_OF_BUCKET/*"
        }
    ]
}
```

- Save policy. You should get a warning that it now has public access
- Public URL should be available to view the deployed application
