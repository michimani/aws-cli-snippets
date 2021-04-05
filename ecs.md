aws ecs help
===

<!-- TOC -->

- [クラスター名に特定の文字列を含むクラスターの ARN を取得](#クラスター名に特定の文字列を含むクラスターの-arn-を取得)
- [クラスター作成](#クラスター作成)
- [タスク定義を登録・登録したタスク定義の ARN を取得](#タスク定義を登録・登録したタスク定義の-arn-を取得)
- [タスクを実行](#タスクを実行)
- [タスクのステータスを取得](#タスクのステータスを取得)
- [実行中タスクに割り当てられたパブリック IP を取得](#実行中タスクに割り当てられたパブリック-ip-を取得)
- [ECS Exec](#ecs-exec)

<!-- /TOC -->

<a id="markdown-クラスター名に特定の文字列を含むクラスターの-arn-を取得" name="クラスター名に特定の文字列を含むクラスターの-arn-を取得"></a>
## クラスター名に特定の文字列を含むクラスターの ARN を取得

```bash
aws ecs list-clusters \
--query "clusterArns[?contains(@, \`${PART_OF_NAME}\`)] | [0]" \
--output text
```

<a id="markdown-クラスター作成" name="クラスター作成"></a>
## クラスター作成

- ECS Exec のログ出力設定を含む (KMS 暗号化は無効)

```bash
aws ecs create-cluster \
--cluster-name ${APP_NAME}-cluster \
--configuration executeCommandConfiguration="{ \
  logging=OVERRIDE,\
  logConfiguration={ \
    cloudWatchLogGroupName=/dev/${APP_NAME}, \
    s3BucketName=${APP_NAME}-${AWS_ACCOUNT_ID}, \
    s3KeyPrefix=test \
  } \
}"
```

<a id="markdown-タスク定義を登録・登録したタスク定義の-arn-を取得" name="タスク定義を登録・登録したタスク定義の-arn-を取得"></a>
## タスク定義を登録・登録したタスク定義の ARN を取得

```bash
TASK_DEFINITION_ARN=$( \
  aws ecs register-task-definition \
  --cli-input-json file://task-definition.json \
  --query "taskDefinition.taskDefinitionArn" \
  --output text) \
&& echo "${TASK_DEFINITION_ARN}" 
```

<a id="markdown-タスクを実行" name="タスクを実行"></a>
## タスクを実行

```bash
aws ecs run-task \
--cluster ${CLUSTER_NAME} \
--task-definition "${TASK_DEFINITION_ARN}" \
--network-configuration "awsvpcConfiguration={subnets=[${SUBNET_ID}],assignPublicIp=ENABLED}" \
--launch-type FARGATE \
--enable-execute-command \
--query "tasks[0].taskArn" \
--output text
```

<a id="markdown-タスクのステータスを取得" name="タスクのステータスを取得"></a>
## タスクのステータスを取得

```bash
aws ecs describe-tasks \
--tasks ${TASK_ARN} \
--cluster ${APP_NAME}-cluster \
--query "tasks[0].lastStatus" \
--output text
```

<a id="markdown-実行中タスクに割り当てられたパブリック-ip-を取得" name="実行中タスクに割り当てられたパブリック-ip-を取得"></a>
## 実行中タスクに割り当てられたパブリック IP を取得

```bash
PUBLIC_IP=$(\
  aws ec2 describe-network-interfaces \
  --network-interface-ids $(\
    aws ecs describe-tasks \
    --tasks ${TASK_ARN} \
    --cluster ${APP_NAME}-cluster \
    --query "tasks[0].attachments[0].details[?name=='networkInterfaceId'].value" \
    --output text) \
  --query "NetworkInterfaces[0].PrivateIpAddresses[0].Association.PublicIp" \
  --output text) \
&& echo "${PUBLIC_IP}"
```

<a id="markdown-ecs-exec" name="ecs-exec"></a>
## ECS Exec

```bash
aws ecs execute-command \
--cluster "${CLUSTER_NAME}" \
--task "${TASK_ARN}" \
--command "/bin/sh" \
--interactive
```