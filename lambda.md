aws lambda
===

## 関数名とランタイムを一覧で取得

```bash
aws lambda list-functions \
--query "sort_by(Functions,&to_string(Runtime))[].{FnName:FunctionName,FnRuntime:Runtime}"
```

## 指定したランタイムの関数名を一覧で取得

```bash
aws lambda list-functions \
--query "Functions[?contains(to_string(Runtime), \`${RUNTIME}\`)].FunctionName
```

## ECR イメージから関数を作成

```bash
aws lambda create-function \
--function-name "${FUNCTION_NAME}" \
--package-type "Image" \
--code "ImageUri=${ECR_IMAGE_URI}" \
--timeout 30 \
--role "${ROLE_ARN}" \
--runtime "${RUNTIME}"
```

## 関数を実行

```bash
aws lambda invoke \
--function-name "${FUNCTION_NAME}" \
--invocation-type RequestResponse
```


## S3 イベントトリガーを追加

```bash
aws lambda add-permission \
--function-name ${FUNCTION_NAME} \
--statement-id "s3-invoke-lambda-function" \
--action "lambda:InvokeFunction" \
--principal "s3.amazonaws.com" \
--source-arn ${INPUT_BUCKET_ARN}
```
