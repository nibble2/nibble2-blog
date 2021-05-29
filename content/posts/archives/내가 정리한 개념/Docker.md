---
author: "Nibble"
title: "Docker로 MongoDB를 사용할 때 주의할 점"
date: "2021-05-26"
tag: "Docker"
categories: "내가 정리한 개념"
ShowToc: false
ShowBreadCrumbs: false
---

# 상황
모든 프로그램은 보통 포트도 함께 설정이 된다. 나의 Demon MongoDB:27017(local) vs Docker MongoDB:27017가 충돌되어 DB에 접속하지 못하는 상황이 발생하였다.


1. docker-compose.yml 포트 변경
- 변경된 포트를 사용하여 docker mongoDB로 접근한다.
- 나의 local mongoDB PORT : 27017
- 도커 MONGODB PORT로 접속하는 포트 : 27018
  
  - 주의할점은 도커네의 몽고디비 포트는 27017이다
        
<br />

---
## 토론
- [과연 도커(Docker) 컨테이너를 통해 데이터베이스를 운영하는 게 좋은 방법일까?](https://this-programmer.tistory.com/entry/%EA%B3%BC%EC%97%B0-%EB%8F%84%EC%BB%A4Docker-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EB%A5%BC-%ED%86%B5%ED%95%B4-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%9A%B4%EC%98%81%ED%95%98%EB%8A%94-%EA%B2%8C-%EC%A2%8B%EC%9D%80-%EB%B0%A9%EB%B2%95%EC%9D%BC%EA%B9%8C)

## VS

- 그러나 데이터 베이스 영속성이 문제라면 볼륨을 맺어서 영속성을 유지시켜주면 되는 거고 한 서버에 프로그램이 여러개 돌 경우 하나로 둔 디비가 고장난다면 프로그램 전부 고장날수도 있다. 프로그램과 디비를 1:1 구성했을 경우 디비 구축도 간단하고 기존 시스템에 추가적인 디비 구성이 필요해도 서로 간섭하지 않고 단독적으로 시스템 관리가 가능해진다.