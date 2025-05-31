aws ec2
===

## AmazonLinux 2 の最新のイメージ ID を取得

```bash
IMAGE_ID=$(\
  aws ec2 describe-images \
  --owners amazon \
  --filters 'Name=name,Values=amzn2-ami-hvm-2.0.*-x86_64-gp2' \
  --query 'reverse(sort_by(Images,&CreationDate))[0].ImageId' \
  --output text) \
&& echo "${IMAGE_ID}"
```

## EC2 インスタンスを起動

```bash
aws ec2 run-instances \
--image-id "${IMAGE_ID}" \
--instance-type "${INSTANCE_TYPE}" \
--tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=${INSTANCE_NAME}}]"
--count 1
```

## EC2 インスタンスの起動状況を確認

```bash
aws ec2 describe-instances \
--filters Name=tag-key,Values=Name Name=tag-value,Values=${INSTANCE_NAME} \
--query "Reservations[*].Instances[].State.Name" \
--output text
```

## EC2 インスタンス名からインスタンス ID を取得

```bash
INSTANCE_ID=$(\
  aws ec2 describe-instances \
  --filters Name=tag-key,Values=Name Name=tag-value,Values=${INSTANCE_NAME} \
  --query "Reservations[*].Instances[].InstanceId" \
  --output text \
) && echo "${INSTANCE_ID}"
```

## EC2 インスタンスを終了

```bash
aws ec2 terminate-instances \
--instance-ids ${INSTANCE_ID}
```