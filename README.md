# GitHub Actions CI/CD  
https://zenn.dev/soshimiyamoto/articles/d435cb4cba6102

## AWS CloudFormation
- テンプレート検証
    ```
    aws cloudformation validate-template --template-body file://./cloudformation/network.yml
    ```

- スタック作成
    ```
    aws cloudformation create-stack --stack-name matsuo-stack --template-body file://./cloudformation/network.yml
    ```

- スタック削除
    ```
    aws cloudformation delete-stack --stack-name matsuo-stack
    ```

## Dockerfile
- Dockerイメージビルド
    ```
    cd github-actions-django-ecs
    docker build -t djangoimage .
    docker images
    ```
- Dockerコンテナ起動
    ```
    docker run --rm -p 8080:8080 djangoimage
    ```

## Docker Compose
- 起動
    ```
    docker-compose up -d
    ```
- 停止
    ```
    docker-compose down
    ```

## CI/CD
- ECRリポジトリ作成
    ```
    aws cloudformation create-stack --stack-name matsuo-ecr-stack --template-body file://./cloudformation/ecr.yml
    ```

- ECR プッシュコマンド
    ```
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 458247080796.dkr.ecr.ap-southeast-1.amazonaws.com

    docker build -t matsuo0914-django_app .

    docker tag matsuo0914-django_app:latest 458247080796.dkr.ecr.ap-southeast-1.amazonaws.com/matsuo0914-django_app:latest

    docker push 458247080796.dkr.ecr.ap-southeast-1.amazonaws.com/matsuo0914-django_app:latest
    ```

- ECS ネットワーク設定
    ```
    aws cloudformation create-stack --stack-name matsuo-stack --template-body file://./cloudformation/network.yml --capabilities CAPABILITY_NAMED_IAM 
    ```

- GitHub Secret設定
    - AWS_ACCESS_KEY_ID
    - AWS_SECRET_ACCESS_KEY
    - AWS_ECR_REPO_NAME
    - DJANGO_SECRET_KEY  

- task-definition.json作成
    ```
    aws ecs describe-task-definition --task-definition matsuo-stack-django-app --query taskDefinition > task-definition.json
    ```

- Git Push