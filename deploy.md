# Finquo Junior App Infra

### AWS Account

1. Go to [AWS](https://ap-south-1.signin.aws.amazon.com/)
2. Create or Login to root account
3. Create a new user in [IAM](https://ap-south-1.console.aws.amazon.com/iam)
4. Attach "AdministratorAccess" policy to user
5. After creation, go to the user's Security credentials section
6. Create an "Access keys" with use case "Command Line Interface (CLI)"
7. Download the .csv file containing the access key ID & Secret
8. Create new S3 bucket for saving opentofu state

```bash
aws s3api create-bucket --bucket finquo-app-opentofu-state --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1
```

### Deploy for the First Time

How to trigger deployment of project for the first time?

0. Go to GitHub settings
1. Go to Actions and allow all actions and reusable workflows in general permission.
2. Go to Environments and create new environment called "production"
3. Add AWS secrets and other necessary env-keys mentioned in `.env.schema` file

```env
AWS_ACCESS_KEY_ID=<access-key-ID>
AWS_SECRET_ACCESS_KEY=<secret-access-key>
GH_TOKEN=<github_fine-grained_token>
```

4. That's all

### Redeploy

How to trigger a redeployment?

0. Make sure to do all of the above steps
1. Just push commit changes to this repo from `main` local branch
2. Or merge a pull request to `main` origin branch
3. That's all
