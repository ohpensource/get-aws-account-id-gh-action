# get-aws-account-id-gh-action
Convenient way of retrieving aws account id

### github-action

This action retrieves aws account id based on account prefix, stage and client. The (required) inputs are:

- _account-prefix_: Identification of baseline - usually ohp (ohpen cloud) or dvc (davinci cloud).
- _client_: Name of the client.
- _stage_: Stage (dev, tst, acc, prd).
- _aws-access-key_: AWS access key for accessing organizations.
- _aws-secret-key_: AWS secret key for accessing organizations.
- _aws-role-arn_: AWS role arn for accessing organizations.

Outputs:

- _aws-account-id_: AWS account id.
- _aws-account-tag_: AWS account tag used for retrieving id.

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
        with:
          account-prefix: ${{ inputs.account_prefix }}
          client: ${{ inputs.client }}
          stage: ${{ inputs.stage }}
          aws-access-key: $COR_AWS_ACCESS_KEY_ID
          aws-secret-key: $COR_AWS_SECRET_ACCESS_KEY
          aws-role-arn: $AUTOMATION_ROLE_ARN
      - name: Preparing deployment parameters
        id: deployment_parameters
        run: |
          echo "Tag used for retrieving account id: ${{steps.aws_account.outputs.aws-account-tag}}"
          TFVARS="deployment-conf/terraform.tfvars.json"
          sed -i "s#<account_id>#${{steps.aws_account.outputs.aws-account-id}}#g" $TFVARS
```

### License Summary

This code is made available under the MIT license. Details [here](LICENSE).