# About AWS accounts

This company has many AWS accounts, which can be identified through an account ID or an account alias -
"country" is also used internall. Some examples of different AWS accounts we own are the accounts for:
- USA where employees have IAM roles,
- Greece
- Data, a worldwide account related to data infrastructure services
- Dev for development purposes.

The Kubeflow cluster uses AWS EKS in the AWS data account. Since a user in the US account is independent of the user in the data, even if both users are john.doe, 
simply because they belong to different AWS accounts, the permissions of one person in one AWS account are not necessarily the same as in the other account.

When opening the Kubeflow page, a new user account that actually is an IAM role, is created in the designated AWS data account.

## Checking AWS permissions

To check if an user has any AWS IAM role in the selected country and data AWS accounts, do as the following:
1. Enter https://thiscompany.okta.com/app/UserHome
2. Choose AWS
3. Select a role to choose from the `data-role or us-role`. If something is different, the user
probably don't have two or more AWS roles.

To check your AWS IAM role permissions for the AWS data account, open a new terminal in a
notebook server in https://prod-ds-kubeflow.thiscompany.world/ and run:

`aws iam list-attached-role-policies 
--role-name "<your.name>-data-role" 
--query 'AttachedPolicies[*].[PolicyName]' 
--output text |
sort`

Replace <your.name> by the AWS user, for instance, `--role-name jane.doe-data-role`. To run the same command locally, add the aws parameter `--profile data-prod`.

## Missing permissions

When receiving AccessDenied error and lack permissions, go back to the previous section. 
Assure to attach the appropriate groups to the AWS IAM role.

If the AWS data role has just been created, it will have only the policy casual-dev. To attach
more permissions, follow the instructions from the Permissions spreadsheet - AWS.
