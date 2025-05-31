aws iam
===

## ポリシー名を指定して ARN を取得

- 目的のポリシーに応じて `--path-prefix` を変える

```bash
POLICY_ARN=$(\
  aws iam list-policies \
  --path-prefix "/service-role/" \
  --max-items 1000 \
  --query "Policies[?PolicyName == \`${POLICY_NAME}\`].Arn" \
  --output text )\
&& echo "${POLICY_ARN}"
```


## IAM ロールを作成 (for ECS タスクロール)

1. AssumeRole ポリシードキュメントを作成 (JSON)
2. ロールを作成
3. アタッチするポリシーを作成
4. 作成したロールにポリシーをアタッチ


### 1. AssumeRole ポリシードキュメントを作成 (JSON)

```bash
cat <<EOF > assume-role-policy-doc-for-task-role.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "ecs-tasks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
```

### 2. ロールを作成

```bash
TASK_ROLE_ARN=$( \
  aws iam create-role \
  --role-name ${APP_NAME}-task-role \
  --assume-role-policy-document file://assume-role-policy-doc-for-task-role.json \
  --query "Role.Arn" \
  --output text ) \
&& echo "${TASK_ROLE_ARN}"
```

### 3. アタッチするポリシーを作成

```bash
cat <<EOF > policy-for-task-role.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetEncryptionConfiguration"
      ],
      "Resource": "arn:aws:s3:::${APP_NAME}-${AWS_ACCOUNT_ID}"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::${APP_NAME}-${AWS_ACCOUNT_ID}/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:DescribeLogGroups"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogStream",
        "logs:DescribeLogStreams",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:ap-northeast-1:${AWS_ACCOUNT_ID}:log-group:/dev/${APP_NAME}:*"
    }
  ]
}
EOF
```

### 4. 作成したロールにポリシーをアタッチ

```bash
TASK_ROLE_POLICY_ARN=$(\
  aws iam create-policy \
  --policy-name "${APP_NAME}-task-role-policy" \
  --path "/sample/" \
  --policy-document file://policy-for-task-role.json \
  --query "Policy.Arn" \
  --output text )\
&& echo ${TASK_ROLE_POLICY_ARN}
```