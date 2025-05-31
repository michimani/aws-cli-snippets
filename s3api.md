aws s3api
===

## オブジェクトをタグ・メタデータ付きで作成

```bash
aws s3api put-object \
--bucket "${S3_BUCKET_NAME}" \
--key "${FILE_NAME}" \
--body ./"${FILE_NAME}" \
--metadata custom-metadata-1=value1,custom-metadata-2=value2,Custom-Metadata-3=value3 \
--cache-control "public, max-age=1209600" \
--content-type "text/plain8" \
--tagging "TagKey1=TagValue1"
```

## オブジェクトのタグを取得

```bash
aws s3api get-object-tagging \
--bucket ${S3_BUCKET_NAME} \
--key "${KEY}"
```

## バージョニングされたオブジェクトのバージョンを取得

```bash
aws s3api list-object-versions \
--bucket "${S3_BUCKET_NAME}" \
--prefix "${KEY_PREFIX}"
```

## バケットレプリケーションの設定を追加

```bash
cat << EOF > ./replication-doc.json
{
  "Role": "${S3_REPLICA_ROLE_ARN}",
  "Rules": [
    {
      "Status": "Enabled",
      "Priority": 1,
      "DeleteMarkerReplication": { "Status": "Disabled" },
      "Filter" : { "Prefix": ""},
      "Destination": {
        "Bucket": "arn:aws:s3:::${S3_BUCKET_NAME}"
      }
    }
  ]
}
EOF
```

```bash
aws s3api put-bucket-replication \
--bucket ${S3_BUCKET_NAME} \
--replication-configuration file://replication-doc.json
```