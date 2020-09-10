Creates a Terraform environment.

# Prerequisites

The Cloudify Terraform Integration blueprint must be uploaded to Cloudify Manager, with the ID
`cloudify-terraform-1.0`.

## Environment Variables

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

# Notes

* `environment-mapping` is whitespace-delimited list of strings. For each item:
    * If it is of the form `k=v`, then the value of the environment variable `k` is passed
      as environment variable `v` to Terraform.
    * Otherwise, let the value be `x`; then the environment variable `x` is passed as-is
      to Terraform.

    This effectively passes environment variables, visible to the CI/CD platform,
    as environment variables to Terraform. Useful, among others, for
    passing Cloud credentials - stored in the CI/CD platform - to Terraform

# More Information

Refer to [Cloudify CI/CD Integration](https://docs.cloudify.co/latest/working_with/integration/) for additional information about
Cloudify's integration with CI/CD tools.
