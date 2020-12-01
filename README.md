# Custom docker

```bash
docker build -t flask-lambda .
```

https://www.bogotobogo.com/DevOps/Docker/Docker-Flask-ALB-ECS.php

docker run -d -p 8080:8080 flask-lambda

$(aws ecr get-login --no-include-email --region eu-central-1 --profile serverless-bert)
