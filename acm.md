aws acm
===

## 指定したドメインに対する Certificate の ARN を取得

```bash
aws acm list-certificates \
--query "CertificateSummaryList[?DomainName=='${DOMAIN_NAME}'].CertificateArn" \
--output text
```

## 指定したドメインに対する Certificate の認証ステータスを取得

```bash
aws acm describe-certificate \
--certificate-arn "$(\
  aws acm list-certificates \
  --query "CertificateSummaryList[?DomainName=='${DOMAIN_NAME}'].CertificateArn" \
  --output text \
)" \
--query "Certificate.DomainValidationOptions[0].ValidationStatus" \
--output text
```


## 指定したドメインに対する Certificate のステータスを取得

```bash
aws acm describe-certificate \
--certificate-arn "$(\
  aws acm list-certificates \
  --query "CertificateSummaryList[?DomainName=='${DOMAIN_NAME}'].CertificateArn" \
  --output text \
)" \
--query "Certificate.Status" \
--output text
```