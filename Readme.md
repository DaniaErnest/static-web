
# aws-lambda-function write the records to Axiom

A [Terraform] module for deploying and managing [Serverless Lambda Functions] on [Amazon Web Services (AWS)][AWS].

---

The axiom-lambda-extension can send logs and platform events of your Lambda function to [Axiom](https://axiom.co/). Axiom will detect the extension and provide you with quick filters and a dashboard.

With the Axiom Lambda extension, you can forget about the extra configuration of CloudWatch and subscription filters.

**Note:** After the Axiom Lambda extension is installed, the Lambda service will still send logs to CloudWatch Logs. 

To disable the CloudWatch logging, follow the steps below:

* Install the Axiom Lambda extension;
* Make sure everything is working properly in Axiom;
* Disable the permissions for CloudWatch.

For more detail on how to disable the CloudWatch logging, see the [Axiom documentation](https://www.axiom.co/docs/send-data/lambda#disable-cloudwatch-logging).

## Quickstart

1. Set these environment variables on your function:

   - `AXIOM_DATASET`: The dataset name to send logs to. Learn more about creating a dataset [here](https://www.axiom.co/docs/reference/settings#dataset)
   - `AXIOM_TOKEN`: The Axiom API token (needs ingest permission into the dataset above). Learn more about creating tokens [here](https://www.axiom.co/docs/restapi/token#creating-an-access-token)

**note** the extensions will not work without correct credentials, but it will not crash your function. If you want it to crash for testing purposes
check the [Troubleshooting](#troubleshooting) section below.


* Use the **latest** version number specified on the [Releases](https://github.com/axiomhq/axiom-lambda-extension/releases) page for the `VERSION` parameter. For example, `4`.
* For more detail on `AWS_REGION` and `ARCH` parameters, expand the table below:

<details>
<summary>
All Lambda Layers
</summary>

|  Region | arm64 | x86_64 |
|---------|--------|---------|
| us-west-1 | `arn:aws:lambda:us-west-1:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:us-west-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| us-west-2  | `arn:aws:lambda:us-west-2:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:us-west-2:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| us-east-1 | `arn:aws:lambda:us-east-1:694952825951:layer:axiom-extension-arm64:<VERSION>` | `arn:aws:lambda:us-east-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| us-east-2 | `arn:aws:lambda:us-east-2:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:us-east-2:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| eu-west-1 | `arn:aws:lambda:eu-west-1:694952825951:layer:axiom-extension-arm64:<VERSION>` | `arn:aws:lambda:eu-west-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| eu-west-2 | `arn:aws:lambda:eu-west-2:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:eu-west-2:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| eu-west-3  | `arn:aws:lambda:eu-west-3:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:eu-west-3:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| eu-north-1 | `arn:aws:lambda:eu-north-1:694952825951:layer:axiom-extension-arm64:<VERSION>` | `arn:aws:lambda:eu-north-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| eu-central-1 | `arn:aws:lambda:eu-central-1:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:eu-central-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ca-central-1 | `arn:aws:lambda:ca-central-1:694952825951:layer:axiom-extension-arm64:<VERSION>` | `arn:aws:lambda:ca-central-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| sa-east-1 | `arn:aws:lambda:sa-east-1:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:sa-east-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ap-south-1  | `arn:aws:lambda:ap-south-1:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:ap-south-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ap-southeast-1 | `arn:aws:lambda:ap-southeast-1:694952825951:layer:axiom-extension-arm64:<VERSION>` | `arn:aws:lambda:ap-southeast-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ap-southeast-2 | `arn:aws:lambda:ap-southeast-2:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:ap-southeast-2:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ap-northeast-1 | `arn:aws:lambda:ap-northeast-1:694952825951:layer:axiom-extension-arm64:<VERSION>` | `arn:aws:lambda:ap-northeast-1:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ap-northeast-2 | `arn:aws:lambda:ap-northeast-2:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:ap-northeast-2:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
| ap-northeast-3  | `arn:aws:lambda:ap-northeast-3:694952825951:layer:axiom-extension-arm64:<VERSION>` |  `arn:aws:lambda:ap-northeast-3:694952825951:layer:axiom-extension-x86_64:<VERSION>` |
</details>


## Terraform Example

 Using Terraform to hook up your Lambda with Axiom Lambda:



Using [AWS lambda module](https://registry.terraform.io/modules/terraform-aws-modules/lambda/aws/latest):
```tf
module "lambda_triggered" {
  source = "../lambda"
  function_name = "axiom_lambda"
  bucket_arn = module.s3_bucket.arn
  bucket_id = module.s3_bucket.id
  filename = local.module_source_path
  source_path = local.module_source_hash 
  handler = "axiom_lambda.lambda_handler"
}
```



## Module Features

In contrast to the plain `terraform_resource` resource this module has better features.
While all security features can be disabled as needed best practices
are pre-configured.

These are some of our custom features:

- **Standard Module Features**:
  Deploy a local deployment package to AWS Lambda
  Deploy a deployment package located in S3 to AWS Lambda

- **Extended Module Features**:
  Aliases, Permissions, VPC Config

- *Features not yet implemented*:
  Event Source Mapping,
  Event Invoke Config
  Layer Versions,
  Provisioned Concurrency Config

## Getting Started

Most basic usage just setting required arguments:

```tf
module "lambda_triggered" {
  source = "../lambda"
  function_name = "axiom_lambda"
  bucket_arn = module.s3_bucket.arn
  bucket_id = module.s3_bucket.id
  filename = local.module_source_path
  source_path = local.module_source_hash 
  handler = "axiom_lambda.lambda_handler"
}
```

**Note**: This module expects the `ARN of an existing s3-Bucket`, `ID of an existing s3-Bucket`, `filename`, `source_path` and `handler` through the variable.


## Module Argument Reference

See [variables.tf] and [examples/] for details and use-cases.

### Top-level Arguments

#### Module Configuration

- [**`module_enabled`**](#var-module_enabled): *(Optional `bool`)*<a name="var-module_enabled"></a>

  Specifies whether resources in the module will be created.

  Default is `true`.

- [**`module_tags`**](#var-module_tags): *(Optional `map(string)`)*<a name="var-module_tags"></a>

  A map of tags that will be applied to all created resources that accept tags.
  Tags defined with 'module_tags' can be overwritten by resource-specific tags.

  Default is `{}`.

- [**`module_depends_on`**](#var-module_depends_on): *(Optional `list(dependencies)`)*<a name="var-module_depends_on"></a>

  A list of dependencies. Any object can be _assigned_ to this list to define a hidden external dependency.

#### Lambda Function Resource Configuration

- [**`function_name`**](#var-function_name): *(**Required** `string`)*<a name="var-function_name"></a>

  A unique name for the Lambda function.

- [**`handler`**](#var-handler): *(Optional `string`)*<a name="var-handler"></a>

  The function entrypoint in the code. This is the name of the method in the code which receives the event and context parameter when this Lambda function is triggered.

- [**`role_arn`**](#var-role_arn): *(**Required** `string`)*<a name="var-role_arn"></a>

  The ARN of the policy that is used to set the permissions boundary for the IAM role for the Lambda function.

- [**`runtime`**](#var-runtime): *(**Required** `string`)*<a name="var-runtime"></a>

  The runtime the Lambda function should run in. A list of all available runtimes can be found here: https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html

  Default is `"[]"`.

- [**`layer_arns`**](#var-layer_arns): *(Optional `set(string)`)*<a name="var-layer_arns"></a>

  Set of Lambda Layer Version ARNs (maximum of 5) to attach to your Lambda function.
  For details see https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html

  Default is `[]`.

  Default is `-1`.

- [**`s3_bucket`**](#var-s3_bucket): *(Optional `string`)*<a name="var-s3_bucket"></a>

  The S3 bucket location containing the function's deployment package.
  Conflicts with `filename`. This bucket must reside in the same AWS region where you are creating the Lambda function.

- [**`source_code_hash`**](#var-source_code_hash): *(Optional `string`)*<a name="var-source_code_hash"></a>

  Used to trigger updates. Must be set to a base64-encoded SHA256 hash of the package file specified with either `filename` or `s3_key`.

- [**`environment_variables`**](#var-environment_variables): *(Optional `map(string)`)*<a name="var-environment_variables"></a>

  A map of environment variables to pass to the Lambda function.
  AWS will automatically encrypt these with KMS if a key is provided and decrypt them when running the function.

  Default is `{}`.

- [**`kms_key_arn`**](#var-kms_key_arn): *(Optional `string`)*<a name="var-kms_key_arn"></a>

  The ARN for the KMS encryption key that is used to encrypt environment variables. If none is provided when environment variables are in use, AWS Lambda uses a default service key.

- [**`filename`**](#var-filename): *(Optional `string`)*<a name="var-filename"></a>

  The path to the local .zip file that contains the Lambda function source code.

- [**`timeout`**](#var-timeout): *(Optional `number`)*<a name="var-timeout"></a>

  The amount of time the Lambda function has to run in seconds. For details see https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html

  Default is `3`.


- [**`s3_key`**](#var-s3_key): *(Optional `string`)*<a name="var-s3_key"></a>

  The S3 key of an object containing the function's deployment package.
  Conflicts with `filename`.

- [**`s3_object_version`**](#var-s3_object_version): *(Optional `string`)*<a name="var-s3_object_version"></a>

  The object version containing the function's deployment package.
  Conflicts with `filename`.


- [**`memory_size`**](#var-memory_size): *(Optional `number`)*<a name="var-memory_size"></a>

  Amount of memory in MB the Lambda function can use at runtime.
  For details see https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html

  Default is `128`.

- [**`permissions`**](#var-permissions): *(Optional `list(permission)`)*<a name="var-permissions"></a>

  A list of permission objects of external resources (like a CloudWatch Event Rule, SNS, or S3) that should have permission to access the Lambda function.

  Default is `[]`.

  Example:

  ```hcl
  permissions = [
    {
      statement_id = "AllowExecutionFromSNS"
      principal    = "sns.amazonaws.com"
      source_arn   = aws_sns_topic.lambda.arn
    }
  ]
  ```

  Each `permission` object in the list accepts the following attributes:

  - [**`statement_id`**](#attr-permissions-statement_id): *(**Required** `string`)*<a name="attr-permissions-statement_id"></a>

    A unique statement identifier.

  - [**`action`**](#attr-permissions-action): *(**Required** `string`)*<a name="attr-permissions-action"></a>

    The AWS Lambda action you want to allow in this statement. (e.g. `lambda:InvokeFunction`)

  - [**`principal`**](#attr-permissions-principal): *(**Required** `string`)*<a name="attr-permissions-principal"></a>

    The principal who is getting this permission. e.g. `s3.amazonaws.com`, an AWS account ID, or any valid AWS service principal such as `events.amazonaws.com` or `sns.amazonaws.com`.

  - [**`event_source_token`**](#attr-permissions-event_source_token): *(Optional `string`)*<a name="attr-permissions-event_source_token"></a>

    The Event Source Token to validate. Used with Alexa Skills.

  - [**`qualifier`**](#attr-permissions-qualifier): *(Optional `string`)*<a name="attr-permissions-qualifier"></a>

    Query parameter to specify function version or alias name.
    The permission will then apply to the specific qualified ARN. e.g. `arn:aws:lambda:aws-region:acct-id:function:function-name:2`.

  - [**`source_account`**](#attr-permissions-source_account): *(Optional `string`)*<a name="attr-permissions-source_account"></a>

    This parameter is used for S3 and SES.
    The AWS account ID (without a hyphen) of the source owner.

  - [**`source_arn`**](#attr-permissions-source_arn): *(Optional `string`)*<a name="attr-permissions-source_arn"></a>

    When the principal is an AWS service, the ARN of the specific resource within that service to grant permission to. Without this, any resource from principal will be granted permission – even if that resource is from another account.
    For S3, this should be the ARN of the S3 Bucket.
    For CloudWatch Events, this should be the ARN of the CloudWatch Events Rule.
    For API Gateway, this should be the ARN of the API, as described in
    https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-control-access-using-iam-policies-to-invoke-api.html.


## Module Outputs

The following attributes are exported by the module:

- [**`function`**](#output-function): *(`object(function)`)*<a name="output-function"></a>

  All outputs of the `aws_lambda_function` resource."

- [**`aliases`**](#output-aliases): *(`map(alias)`)*<a name="output-aliases"></a>

  A map of all created `aws_lambda_alias` resources keyed by name.

- [**`permissions`**](#output-permissions): *(`list(permission)`)*<a name="output-permissions"></a>

  A map of all created `aws_lambda_permission` resources keyed by
  `statement_id`.

- [**`module_enabled`**](#output-module_enabled): *(`bool`)*<a name="output-module_enabled"></a>

  Whether this module is enabled.

- [**`module_inputs`**](#output-module_inputs): *(`map(module_inputs)`)*<a name="output-module_inputs"></a>

  A map of all module arguments. Omitted optional arguments will be
  represented with their actual defaults.

- [**`module_tags`**](#output-module_tags): *(`map(string)`)*<a name="output-module_tags"></a>

  The map of tags that are being applied to all created resources that
  accept tags.

