# Self-Signed Certificate Generator Using AWS KMS

This script generates a self-signed certificate using an AWS KMS key. The generated certificate can be saved to a local file, AWS SSM Parameter Store, AWS Secrets Manager, S3 bucket, HTTP(S) endpoint, SQS, SNS, DynamoDB, or output as JSON. The script supports RSA and ECC keys and dynamically selects the appropriate signing algorithm based on the KMS key's specifications.

## Features

- Supports RSA and ECC keys
- Dynamically selects the appropriate signing algorithm based on KMS key specifications
- Multiple output formats, including local file, JSON, AWS SSM Parameter Store, AWS Secrets Manager, S3 bucket, HTTP(S) endpoint, SQS, SNS, and DynamoDB
- Optionally sets certificate details such as country, state, locality, organization, organizational unit, email, and subject alternative names (SANs)
- Ensures Google Play Store compatibility with RSA keys

## Prerequisites

- AWS CLI
- OpenSSL
- jq
- curl (only if using HTTP output)

## Required IAM Permissions

- `kms:DescribeKey`
- `kms:GetPublicKey`
- `kms:Sign`
- `ssm:GetParameter` (if using SSM output)
- `ssm:PutParameter` (if using SSM output)
- `secretsmanager:DescribeSecret` (if using Secrets Manager output)
- `secretsmanager:CreateSecret` (if using Secrets Manager output)
- `secretsmanager:UpdateSecret` (if using Secrets Manager output)
- `secretsmanager:GetSecretValue` (if using Secrets Manager output)
- `s3:PutObject` (if using S3 output)
- `s3:GetObject` (if using S3 output)
- `sqs:SendMessage` (if using SQS output)
- `sns:Publish` (if using SNS output)
- `dynamodb:PutItem` (if using DynamoDB output)

## Usage

```
./generate_self_signed_cert.sh --kms-key-id <KMS_KEY_ID> --cert-common-name <CERT_COMMON_NAME> [OPTIONS]
```

### Required Arguments

- `--kms-key-id, -k`: The ID of the KMS key to use for signing.
- `--cert-common-name, -c`: The common name (CN) for the certificate.

### Optional Arguments

- `--validity-days, -v`: The number of days the certificate is valid. Defaults to 9125 (25 years).
- `--output, -o`: The name of the output file or the prefix '-', 'file:', 'http:', 'json:', 'jsonfile:', 's3:', 's3:json:', 'secretsmanager:', 'secretsmanager:json:', 'sns:', 'snsjson:', 'sqs:', 'sqsjson:', 'ssm:', 'ssm:json:', 'dynamodb:', 'dynamodbjson:'. Defaults to 'file:self_signed_certificate.pem'.
- `--cert-country`: The country name (C) for the certificate.
- `--cert-state`: The state or province name (ST) for the certificate.
- `--cert-locality`: The locality or city name (L) for the certificate.
- `--cert-org`: The organization name (O) for the certificate.
- `--cert-org-unit`: The organizational unit name (OU) for the certificate.
- `--cert-email`: The email address for the certificate.
- `--cert-san`: The subject alternative name (SAN) for the certificate. This option can be repeated.
- `--google-play-store-spec`: Ensure the key type is RSA for Google Play Store compatibility.

### Output Options

- `-`: Output the certificate to stdout (not in JSON format).
- `file:<filename>`: Save the certificate to a local file. (Default)
- `http:<post_url>`: Post the certificate to a specified HTTP(S) endpoint.
- `http:<post_url>|<cert_key>`: Post the certificate to a specified HTTP(S) endpoint, optionally specifying a custom key name.
- `json:<cert_key>`: Output the certificate as a JSON object to stdout with the specified key.
- `jsonfile:<filename>`: Save the certificate as a JSON object to a local file.
- `jsonfile:<filename>|<cert_key>`: Save the certificate as a JSON object to a local file, optionally specifying a custom key name.
- `s3:<s3_region>|<s3_bucket>|<s3_key>`: Save the certificate to an S3 bucket.
- `s3:json:<s3_region>|<s3_bucket>|<s3_key>`: Save the certificate as a JSON object to an S3 bucket.
- `s3:json:<s3_region>|<s3_bucket>|<s3_key>|<cert_key>`: Save the certificate as a JSON object to an S3 bucket, optionally specifying a custom key name.
- `secretsmanager:<name>`: Save the certificate to AWS Secrets Manager.
- `secretsmanager:json:<name>`: Save the certificate as a JSON object to AWS Secrets Manager.
- `secretsmanager:json:<name>|<cert_key>`: Save the certificate as a JSON object to AWS Secrets Manager, optionally specifying a custom key name.
- `sns:<topic_arn>`: Send the certificate to an SNS topic.
- `snsjson:<topic_arn>`: Send the JSON certificate to an SNS topic.
- `sqs:<queue_url>`: Send the certificate to an SQS queue.
- `sqsjson:<queue_url>`: Send the JSON certificate to an SQS queue.
- `ssm:<parameter>`: Save the certificate to AWS SSM Parameter Store as a SecureString.
- `ssm:json:<parameter>`: Save the certificate as a JSON object to AWS SSM Parameter Store.
- `ssm:json:<parameter>|<cert_key>`: Save the certificate as a JSON object to AWS SSM Parameter Store, optionally specifying a custom key name.
- `dynamodb:<table>|<payload_key>|<hash_key>|<hash_value>|<sort_key>|<sort_value>`: Save the certificate to a DynamoDB table.
- `dynamodbjson:<table>|<payload_key>|<hash_key>|<hash_value>|<sort_key>|<sort_value>|<cert_key>`: Save the JSON certificate to a DynamoDB table, optionally specifying a custom key name.

## Examples

### Generate a certificate and save it to a local file

```
./generate_self_signed_cert.sh --kms-key-id <KMS_KEY_ID> --cert-common-name <CERT_COMMON_NAME> --output file:my_certificate.pem
```

### Generate a certificate and output it as JSON to stdout

```
./generate_self_signed_cert.sh --kms-key-id <KMS_KEY_ID> --cert-common-name <CERT_COMMON_NAME> --output json:my_cert
```

### Generate a certificate and save it to AWS SSM Parameter Store

```
./generate_self_signed_cert.sh --kms-key-id <KMS_KEY_ID> --cert-common-name <CERT_COMMON_NAME> --output ssm:/my/parameter/name
```

### Generate a certificate and post it to an HTTP endpoint

```
./generate_self_signed_cert.sh --kms-key-id <KMS_KEY_ID> --cert-common-name <CERT_COMMON_NAME> --output http://example.com/api/cert
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
