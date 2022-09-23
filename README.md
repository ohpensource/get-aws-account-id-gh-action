# get-aws-account-id-gh-action
Convenient way of retrieving aws account id

### github-action

This action retrieves aws account id based on account tag. The (required) inputs are:

- _aws-account-tag_: AWS account tag used for retrieving id.
- _aws-access-key_: AWS access key for accessing organizations.
- _aws-secret-key_: AWS secret key for accessing organizations.
- _aws-role-arn_: AWS role arn for accessing organizations.

Outputs:

- _aws-account-id_: AWS account id.

### usage

```yaml
name: Deploy

on:
  workflow_call:
    inputs:
      stage:
        description: 'Stage'
        type: string
        required: true
        default: tst
      client:
        description: "Name of client"
        type: string
        required: true
      account_prefix:
        description: "Prefix of account to deploy to (ohp, dvc)"
        type: string
        required: false
        default: 'dvc'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: ohpensource/get-aws-account-id-gh-action@0.0.0.1
        id: aws_account
        name: Get account id
        env:
          AWS_ACCOUNT_TAG: "${{ inputs.account_prefix }}-${{ inputs.client }}-${{ inputs.stage }}"
        with:
          aws-account-tag: ${{ env.AWS_ACCOUNT_TAG }}
          aws-access-key: $COR_AWS_ACCESS_KEY_ID
          aws-secret-key: $COR_AWS_SECRET_ACCESS_KEY
          aws-role-arn: $AUTOMATION_ROLE_ARN
      - name: Preparing deployment parameters
        id: deployment_parameters
        run: |
          TFVARS="deployment-conf/terraform.tfvars.json"
          sed -i "s#<account_id>#${{steps.aws_account.outputs.aws-account-id}}#g" $TFVARS
```

### License Summary

This code is made available under the MIT license. Details [here](LICENSE).