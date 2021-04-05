aws sts help
===

<!-- TOC -->

- [AWS アカウント ID を取得](#aws-アカウント-id-を取得)

<!-- /TOC -->


<a id="markdown-aws-アカウント-id-を取得" name="aws-アカウント-id-を取得"></a>
### AWS アカウント ID を取得

```bash
AWS_ACCOUNT_ID=$( \
  aws sts get-caller-identity \
  --query 'Account' \
  --output text ) \
&& echo "${AWS_ACCOUNT_ID}"
```

