Tectonic graphical installer IAM prep via Terraform
===================================================

[Tectonic](https://coreos.com/tectonic/) has a graphical installer
which requires a little
[prep work](https://coreos.com/tectonic/docs/latest/install/aws/requirements.html).
The config here will allow you to do the IAM portion of that via
Terraform.

Note: this will store the Tectonic installer IAM user secret access
key in the Terraform state file.  Securing it is left as an exercise
for you, dear reader.

The below assumes you already have the AWS CLI installed.

Clone this repo, edit the `terraform.tfvars` file, then:

```
$ terraform plan -out /tmp/plan.out
$ terraform apply /tmp/plan.out
$ AWS_ACCESS_KEY_ID=$(terraform output access_key_id) \
        AWS_SECRET_ACCESS_KEY=$(terraform output secret_access_key) \
        AWS_DEFAULT_REGION=$(terraform output aws_region) \
        aws sts assume-role \
            --role-arn=$(terraform output installer_role_arn) \
            --role-session-name=$USER-$(TZ=UTC date +%Y%m%dT%H%MZ)
```

Use the resulting `AccessKeyId`, `SecretAccessKey`, and `SessionToken`
in the Tectonic graphical installer.
