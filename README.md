# terraform-plan-on-pr

Run `terraform plan` automatically on every pull request and post the
diff as a comment. Reviewers see exactly what changes before they approve.

No AWS keys stored in GitHub. The workflow assumes an IAM role at runtime
using GitHub OIDC.

## How it works

1. You open a PR that touches a `.tf` file.
2. GitHub Actions checks formatting, initialises, and validates.
3. It runs `terraform plan` and posts the full diff as a sticky PR comment.
4. The reviewer sees exactly what will change before approving. No surprises after merge.

## Setup

1. Create an IAM role trusted by GitHub's OIDC provider (see below).
2. Add the role ARN as a repo secret named `AWS_PLAN_ROLE_ARN`.
3. Open a PR that touches a `.tf` file. The plan shows up as a comment.

### IAM role for OIDC

The workflow assumes a role at runtime instead of storing long-lived AWS
keys. You need a GitHub OIDC identity provider in your AWS account and a
role whose trust policy allows this repo to assume it. Scope the trust
policy down to your repo and (ideally) the `pull_request` context.

## Notes

- Region is hard-coded to `eu-west-1` in the workflow and `main.tf`. Change both to match yours.
- `main.tf` creates a single S3 bucket purely so the plan has something to show. Replace it with your real config.
- The plan step uses `continue-on-error` so a failed plan still posts its output to the PR for debugging.
