aws lambda help
===

<!-- TOC -->

- [関数名とランタイムを一覧で取得](#関数名とランタイムを一覧で取得)
- [指定したランタイムの関数名を一覧で取得](#指定したランタイムの関数名を一覧で取得)
- [ECR イメージから関数を作成](#ecr-イメージから関数を作成)
- [関数を実行](#関数を実行)
- [S3 イベントトリガーを追加](#s3-イベントトリガーを追加)

<!-- /TOC -->

<a id="markdown-関数名とランタイムを一覧で取得" name="関数名とランタイムを一覧で取得"></a>
### 関数名とランタイムを一覧で取得

```bash
aws lambda list-functions \
--query "sort_by(Functions,&to_string(Runtime))[].{FnName:FunctionName,FnRuntime:Runtime}"
```

<a id="markdown-指定したランタイムの関数名を一覧で取得" name="指定したランタイムの関数名を一覧で取得"></a>
### 指定したランタイムの関数名を一覧で取得

```bash
aws lambda list-functions \
--query "Functions[?contains(to_string(Runtime), \`${RUNTIME}\`)].FunctionName
```

<a id="markdown-ecr-イメージから関数を作成" name="ecr-イメージから関数を作成"></a>
### ECR イメージから関数を作成

```bash
aws lambda create-function \
--function-name "${FUNCTION_NAME}" \
--package-type "Image" \
--code "ImageUri=${ECR_IMAGE_URI}" \
--timeout 30 \
--role "${ROLE_ARN}" \
--runtime "${RUNTIME}"
```

<a id="markdown-関数を実行" name="関数を実行"></a>
### 関数を実行

```bash
aws lambda invoke \
--function-name "${FUNCTION_NAME}" \
--invocation-type RequestResponse
```


<a id="markdown-s3-イベントトリガーを追加" name="s3-イベントトリガーを追加"></a>
### S3 イベントトリガーを追加

```bash
aws lambda add-permission \
--function-name ${FUNCTION_NAME} \
--statement-id "s3-invoke-lambda-function" \
--action "lambda:InvokeFunction" \
--principal "s3.amazonaws.com" \
--source-arn ${INPUT_BUCKET_ARN}
```
