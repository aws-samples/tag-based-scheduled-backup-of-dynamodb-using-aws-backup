## Set up scheduled backups for Amazon DynamoDB using AWS Backup – Part 2

## Solution overview

This solution helps you scale backups within your organization by implementing a tag-based backup design for a group of DynamoDB tables. A tag is a label that can be used to categorize resources. A tag consists of a user-defined key and a value. Each time a tag-based backup is run, it scans your AWS account for all resources that match the specified tag value. By using tags, you need to define your backup plan and resource assignment only once.

The steps to implement the solution are:

    + Launch an AWS CloudFormation template to create the required resources.
    + Add tags to the DynamoDB table.
    + Monitor the backup.

## Create the resources for the solution

To get started with this solution, you download a code snippet from GitHub and launch and configure a CloudFormation stack. The snippet defines the backup, including frequency and lifecycle configuration such as number of days before backup transitions to cold storage, and expiration. You can set these parameters to fit your requirements when you create the CloudFormation stack.

```
BackupPlan:
    Type: "AWS::Backup::BackupPlan"
    Properties:
      BackupPlan:
        BackupPlanName: !Sub 'BackupPlan_${AWS::StackName}'
        BackupPlanRule:
          
          - RuleName: "RuleForDailyBackups"
            TargetBackupVault: !Ref BackupVault
            ScheduleExpression: "cron(0 5 ? * * *)"
            StartWindowMinutes: 60
            CompletionWindowMinutes : 120
            Lifecycle:
              DeleteAfterDays:
                Ref: LifecycleDeleteAfterDays
              MoveToColdStorageAfterDays:
                Ref: LifecycleMoveToColdStorageAfterDays

```

## To create AWS resources to build the solution

Before getting started, we set up our resources. For this post, we use the us-east-1 Region.

    1. Deploy the AWS resource using the provided AWS CloudFormation template:
	
![LaunchStack](/image/LaunchStack.png) (https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/template?stackName=tag-based-backup-stack&templateURL=https://aws-blogs-artifacts-public.s3.amazonaws.com/artifacts/DBBLOG-1898/aws_backup.yaml)
	
    2. Enter a Stack name and provide the parameters for your backup strategy.

        + LifecycleDeleteAfterDays: The number of days after creation that a recovery point is deleted.
        + LifecycleMoveToColdStorageAfterDays: The number of days after creation that a recovery point is moved to cold storage.
        + TagKey: The tag key associated with your DynamoDB tables to be backed up.
        + TagValue: The tag value associated with your DynamoDB tables to be backed up. You can use this field to add one tag value. To add multiple tag values, follow the Resource tag documentation to modify the CloudFormation stack file that you downloaded from GitHub.
   
   
   3. Choose Next.
   
   [Stack Details](/image/dbblog-1898-image001.png)

   4. Accept the default settings and choose Next.
   5. Select the checkbox next to I Acknowledge that AWS CloudFormation might create IAM resources and choose Create stack.

[Create Stack](/image/dbblog-1898-image002.png)

    Alternatively , you can download the CloudFormation stack file from GitHub. Sign in to the AWS Management Console and navigate to CloudFormation. Choose Create stack and select With new resources (standard). Select Upload a template file and upload the stack file.


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

