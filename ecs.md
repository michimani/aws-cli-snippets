aws ecs
===

## クラスター名に特定の文字列を含むクラスターの ARN を取得

```bash
aws ecs list-clusters \
--query "clusterArns[?contains(@, \`${PART_OF_NAME}\`)] | [0]" \
--output text
```

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

## タスク定義を登録・登録したタスク定義の ARN を取得

```bash
TASK_DEFINITION_ARN=$( \
  aws ecs register-task-definition \
  --cli-input-json file://task-definition.json \
  --query "taskDefinition.taskDefinitionArn" \
  --output text) \
&& echo "${TASK_DEFINITION_ARN}" 
```

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

## タスクのステータスを取得

```bash
aws ecs describe-tasks \
--tasks ${TASK_ARN} \
--cluster ${APP_NAME}-cluster \
--query "tasks[0].lastStatus" \
--output text
```


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

## ECS Exec

```bash
aws ecs execute-command \
--cluster "${CLUSTER_NAME}" \
--task "${TASK_ARN}" \
--command "/bin/sh" \
--interactive
```