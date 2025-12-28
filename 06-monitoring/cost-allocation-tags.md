# Cost Allocation Tags

- Tags that we can enable to provide additional information for any billing report in AWS
- Cost Allocation Tags need to enabled individually per account or per organization from the management account
- There 2 different form of Cost Allocation Tags:
  - AWS generated - example: `aws:createdBy` or `aws:cloudformation:stack-name`. These are added automatically by AWS if cost allocation tags are enabled
  - User defined tags: `user:something`
- Both type of tags will be visible in AWS cost reports and can be used as a filter
- Cost Allocation Tags appear only in the Billing Console
- After enabling Cost Allocation Tags, it can take up to 24 hours to be visible and active
- Cost Allocation Tags are not added retroactively
