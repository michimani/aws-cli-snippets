aws acm help
===

<!-- TOC -->

- [指定したドメインに対する Certificate の ARN を取得](#指定したドメインに対する-certificate-の-arn-を取得)
- [指定したドメインに対する Certificate の認証ステータスを取得](#指定したドメインに対する-certificate-の認証ステータスを取得)
- [指定したドメインに対する Certificate のステータスを取得](#指定したドメインに対する-certificate-のステータスを取得)

<!-- /TOC -->

<a id="markdown-指定したドメインに対する-certificate-の-arn-を取得" name="指定したドメインに対する-certificate-の-arn-を取得"></a>
## 指定したドメインに対する Certificate の ARN を取得

```bash
aws acm list-certificates \
--query "CertificateSummaryList[?DomainName=='${DOMAIN_NAME}'].CertificateArn" \
--output text
```

<a id="markdown-指定したドメインに対する-certificate-の認証ステータスを取得" name="指定したドメインに対する-certificate-の認証ステータスを取得"></a>
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


<a id="markdown-指定したドメインに対する-certificate-のステータスを取得" name="指定したドメインに対する-certificate-のステータスを取得"></a>
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