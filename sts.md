aws sts
===

## AWS アカウント ID を取得

```bash
AWS_ACCOUNT_ID=$( \
  aws sts get-caller-identity \
  --query 'Account' \
  --output text ) \
&& echo "${AWS_ACCOUNT_ID}"
```
