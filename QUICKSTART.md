# Quick start: inspect before deploying

This repository contains a static sample application and AWS CloudFormation templates.
It does not contain a CDK application or the previously referenced deploy/destroy scripts.
The templates require review and account-specific configuration before deployment.

```sh
git clone https://github.com/AnasGara/Project_ScalableAPP_AWS.git
cd Project_ScalableAPP_AWS
python -m http.server 8000 --bind 127.0.0.1 --directory application
```

Open [the sample application](http://localhost:8000). This serves static files locally;
it does not provision AWS resources or test AWS integration. Stop with Ctrl+C.

## Infrastructure review

The templates are under `cloudformation/`: VPC, application, RDS, CDN/DNS, and monitoring.
Start with the network and inspect exported values and cross-stack imports before
creating any stack. Several imports currently contain literal `${...}` expressions
without `Fn::Sub`; the templates must be corrected and linted before deployment.
The RDS security group also needs review of its imported source security group.

Use [DEPLOYMENT.md](DEPLOYMENT.md) as design notes, not a verified end-to-end procedure.
Review template parameters, region-specific AMIs, IAM permissions, certificates, DNS,
database secret handling, and deletion policies. Validate templates with `cfn-lint`
and inspect change sets before execution. No deployment or cost estimate is verified here.

## Cleanup

There is no `scripts/destroy-all.sh`. If you have deployed stacks, inspect dependencies,
retained resources, database backups, and data-retention requirements before deleting
them in reverse dependency order through CloudFormation. Stopping this local preview
does not remove any AWS resources.
