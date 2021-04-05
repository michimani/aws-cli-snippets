aws route53 help
===

<!-- TOC -->

- [指定したドメインの HostedZoneID を取得](#指定したドメインの-hostedzoneid-を取得)
- [RecordSet を作成](#recordset-を作成)
- [RecordSet を削除](#recordset-を削除)

<!-- /TOC -->

<a id="markdown-指定したドメインの-hostedzoneid-を取得" name="指定したドメインの-hostedzoneid-を取得"></a>
### 指定したドメインの HostedZoneID を取得

```bash
HOSTED_ZONE_ID=$( \
  aws route53 list-hosted-zones \
  --query "HostedZones[?Name=='${HOSTED_DOMAIN}.'].Id" \
  --output text) \
&& echo ${HOSTED_ZONE_ID}
```

<a id="markdown-recordset-を作成" name="recordset-を作成"></a>
### RecordSet を作成

```bash
aws route53 change-resource-record-sets \
--hosted-zone-id ${HOSTED_ZONE_ID} \
--change-batch \
"{
  \"Changes\": [
    {
      \"Action\": \"CREATE\",
      \"ResourceRecordSet\": {
        \"Name\": \"${RECORD_NAME}\",
        \"Type\": \"${RECORD_TYPE}\",
        \"TTL\": 300,
        \"ResourceRecords\": [{\"Value\": \"${RECORD_VALUE}\"}]
      }
    }
  ]
}"
```

<a id="markdown-recordset-を削除" name="recordset-を削除"></a>
### RecordSet を削除

```bash
aws route53 change-resource-record-sets \
--hosted-zone-id ${HOSTED_ZONE_ID} \
--change-batch \
"{
  \"Changes\": [
    {
      \"Action\": \"DELETE\",
      \"ResourceRecordSet\": {
        \"Name\": \"${RECORD_NAME}\",
        \"Type\": \"${RECORD_TYPE}\",
        \"TTL\": 300,
        \"ResourceRecords\": [{\"Value\": \"${RECORD_VALUE}\"}]
      }
    }
  ]
}"
```