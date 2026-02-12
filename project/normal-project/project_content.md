# 프로젝트 목표

PHP 게시판 + MySQL DB 컨테이너화

Docker Hub 이미지 자동 빌드

GitHub Actions CI/CD 자동화

k3s 클러스터에 자동 배포

NodePort로 외부 접근 가능하도록 구성

# 전체 아키텍처

[GitHub]
   ↓ (push)
[GitHub Actions]
   ↓ (Docker build & push)
[Docker Hub]
   ↓ (kubectl set image)
[k3s Cluster on EC2]
   ↓
[NodePort 30081]
   ↓
http://EC2_IP:30081

# 사용 기술 스택

OS	              Ubuntu
Container	        Docker
Orchestration	    k3s (Kubernetes)
CI/CD	            GitHub Actions
Registry	        Docker Hub
Backend	          PHP
DB  	            MySQL 8.0
Cloud	            AWS EC2

# Docker 구성

FROM php:8.2-apache
COPY . /var/www/html
EXPOSE 80

# MySQL Deployment

MySQL 8.0 사용

Secret으로 비밀번호 관리

PVC로 데이터 영속성 유지

ConfigMap으로 init.sql 마운트

# Kubernetes 구성

Namespace 
  - team-4c

Web Deployment
  - replicas: 1
  - image: Docker Hub 이미지
  - containerPort: 80

Service
  - Type: NodePort
  - Port: 80
  - NodePort: 30081

# GitHub Actions CI/CD

main 브랜치 push

Docker 이미지 빌드

Docker Hub push

kubectl set image

rollout status 확인

SHA 태그 기반 배포
  - kubectl set image deployment/web web=${DOCKER_IMAGE}:${GITHUB_SHA}
  - kubectl rollout status deployment/web

# 트러블슈팅 기록

rollout 무한 대기 문제

원인:
  이미지 경로 불일치
  Docker Hub 태그 불일치

해결:
  vars.DOCKER_IMAGE 통일
  SHA 태그 기반 배포 적용
  

DB 연결 실패

에러:
  SQLSTATE[HY000] [2002]

원인:
  localhost 사용 → 소켓 연결 시도

해결:
  host를 mysql 서비스명으로 변경


Access denied

원인:
  MYSQL_PASSWORD 환경변수 미설정

해결:
  Secret 확인
  Deployment env 정상 연결 확인

