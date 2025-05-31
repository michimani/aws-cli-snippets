aws route53
===

## 指定したドメインの HostedZoneID を取得

```bash
HOSTED_ZONE_ID=$( \
  aws route53 list-hosted-zones \
  --query "HostedZones[?Name=='${HOSTED_DOMAIN}.'].Id" \
  --output text) \
&& echo ${HOSTED_ZONE_ID}
```

## RecordSet を作成

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

## RecordSet を削除

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