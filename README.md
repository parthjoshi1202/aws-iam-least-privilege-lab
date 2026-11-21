# AWS IAM Least-Privilege Policy Audit

## Scenario

A draft identity policy granted every Amazon S3 action on every resource. The fictional workload only needed to read evidence objects, so deletion and access to unrelated resources were unnecessary.

## Safe test scope

- Simulated resource: `arn:aws:s3:::amzn-s3-demo-bucket/report.txt`
- No Amazon S3 bucket or object was created.
- Neither policy was attached to a user, group, or role during testing.

## Finding

The original policy used `s3:*` with `Resource` set to `*`. The IAM policy simulator showed that both `s3:GetObject` and `s3:DeleteObject` were allowed against the test object.

Risk: A principal with this policy could read, modify, or delete S3 data far beyond the stated read-only requirement.

## Remediation

The replacement policy allows only `s3:GetObject` and scopes access to objects under `arn:aws:s3:::amzn-s3-demo-bucket/*`.

| Policy | `s3:GetObject` | `s3:DeleteObject` |
| --- | --- | --- |
| Over-permissive baseline | allowed | allowed |
| Least-privilege replacement | allowed | implicitly denied |

IAM Access Analyzer policy validation completed with no remaining syntax errors in the replacement policy.

## Audit evidence

- Event name: `CreatePolicy`
- Event source: `iam.amazonaws.com`
- Region inspected: `us-east-1`
- Customer managed policy: `CloudSecurityEvidenceReadOnly`

The account ID, principal ARN, access key ID, source IP address, and other identity details were intentionally excluded from this public report.

## Cost and cleanup

This lab used no real S3 resources, no CloudTrail trail, no CloudTrail Lake event data store, and no paid IAM Access Analyzer custom policy checks. The customer managed policy was created unattached and can be deleted after the audit evidence is reviewed.
