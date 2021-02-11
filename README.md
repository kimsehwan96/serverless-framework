# ⚡️ Serverless Framework

## ⚡️ 서버리스 프레임워크란?

- Infrastructure as Code 를 쉽게 구성하도록 도와주는 프레임워크입니다.

- 기존 클라우드 서비스를 웹 콘솔을 이용하여 속성들을 정의하고, 설정하고, 배포하였다면
- 이를 코드 기반으로 옮기는 것이 바로 서버리스 프레임워크입니다.

- 비슷한 서비스로는 : 테라폼, 클라우드포메이션(aws)가 있습니다.

## 어떻게 사용하나요?

- 우선 배포하고자 하는 서비스(AWS, GCP)에 해당하는 인증을 자신의 로컬 컴퓨터에 설정한 이후에 배포가 가능하며
- 배포는 다음과 같이 수행 할 수 있습니다.
    - `serverless deploy`
    - `sls deploy`
    - `sls deploy --region ap-northeast-2`
    - `sls deploy --region ap-northeast-2 --stage prod`
    - `sls deploy --region ap-northeast-2 --stage prod --profile myprofile`

## 어떻게 인프라스트럭쳐를 코드처럼 관리 할 수 있나요?

- yml 기반의 템플릿으로 각종 클라우드 서비스들의 이름, 속성, 타입을 정의 할 수 있습니다. 다음은 Public access 가능한 s3 버킷을 정의한 문서입니다.

```yml
service: my-public-bucket-test

provider:
  name: aws
  stage: ${opt:stage, self:custom.defaultStage}
  stackName: ${self:service}-${self:provider.stage}
  imageBucket: ${self:service}-${self:provider.stage}-image-bucket

custom:
  custom.defaultStage: dev

resources:
  Description: "bucket stack"
  Resources:
    imageBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: ${self:provider.imageBucket}
        AccessControl: Private
        CorsConfiguration:
          CorsRules:
            - AllowedOrigins:
                - '*'
              AllowedHeaders:
                - '*'
              AllowedMethods:
                - GET
                - PUT
                - POST
                - DELETE
                - HEAD
              MaxAge: 3000
        #크로스 오리진 정책 설정. 모든 아이피에서 접근  가능.
    imageBucketAllowPublicPolicy:
      Type: AWS::S3::BucketPolicy
      Properties:
        Bucket: ${self:provider.imageBucket}
        PolicyDocument:
          Version: "2012-10-17"
          Statement:
            - Effect: Allow
              Action:
                - "s3:DeleteObject"
                - "s3:GetObject"
                - "s3:ListBucket"
                - "s3:PutObject"
                #버킷에 객체를 삽입, 삭제, 리스트 가능한 권한 설정.
              Resource:
                - arn:aws:s3:::${self:provider.imageBucket}
                - arn:aws:s3:::${self:provider.imageBucket}/*
                #버킷내 모든 리소스를 누구나 접근 가능
              Principal: "*" #모든 사용자가 접근 가능하도록 정의
          

Outputs:
  imageBucket:
    Value:
      Ref: imageBucket
```

## 무엇을 알아야 할 수 있을 까요?

- Cloud Formation을 알아야 쉽게 사용 가능합니다.
- Cloud Formation은 Json 기반으로 클라우드 리소스에 대해서 Template을 만들고, 이를 배포 할 수 있는 컨셉입니다.

- CloudFormation 디렉터리에 해당 내용을 자세히 설명합니다.