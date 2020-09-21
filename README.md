Creates a Terraform environment.

# Environment Variables

This Action uses the Cloudify Profile environment variables described in the official
Cloudify documentation (see [More Information](#more-information) below).

# Inputs

(Certain commonly-used inputs are documented in our official website; see [More Information](#more-information) below)

| Name | Description
|------|------------
| `module-file` | URL/path to Terraform module archive
| `variables-file` | YAML/JSON file containing template variables
| `environment-file` | YAML/JSON file containing environment variables to pass to the Terraform process
| `environment-mapping` | A whitespace-delimited list of strings denoting environment variables to pass through (see below)

## Notes

* `environment-mapping` is whitespace-delimited list of strings. For each item:
    * If it is of the form `k=v`, then the value of the environment variable `k` is passed
      as environment variable `v` to Terraform.
    * Otherwise, let the value be `x`; then the environment variable `x` is passed as-is
      to Terraform.

    This effectively passes environment variables, visible to the CI/CD platform,
    as environment variables to Terraform. Useful, among others, for
    passing Cloud credentials - stored in the CI/CD platform - to Terraform

# Example

```yaml
jobs:
  test_job:
    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Create module archive
        run: tar -cvzf /tmp/terraform-module.tar.gz -C tf/modules/ .
      - name: Upload to S3
        run: aws s3 cp /tmp/terraform-module.tar.gz s3://cloudify-cicd-public/
      - name: Create environment
        uses: cloudify-cosmo/terraform-action@v1.0
        with:
          environment-name: "test-terraform-$GITHUB_RUN_ID"
          module-file: https://cloudify-cicd-public.s3.amazonaws.com/terraform-module.tar.gz
          variables-file: tf/test-params/integration.yaml
          environment-mapping: AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY
          outputs-file: env-data.json
```

Once code is checked out, the Terraform module within the codebase is archived, and
uploaded to an S3 bucket (the `aws` CLI is included with the Cloudify CLI Docker image). This is done
in order to make the Terraform module available for Cloudify Manager to download.

As the Terraform AWS Provider supports obtaining AWS credentials from environment variables,
we define those variables based on GitHub Secrets, and use the `environment-mapping` input so
the variables are passed as-is to the Terraform executable. 

# More Information

Refer to [Cloudify CI/CD Integration](https://docs.cloudify.co/latest/working_with/integration/) for additional information about
Cloudify's integration with CI/CD tools.
