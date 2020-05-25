# Chap 7. MySQL

## MySQL 시작하기

MySQL 실행을 위해 아래의 cmd를 사용한다.  
```shell script
mysql -h localhost -u root -p
```

## Sequelize 이용하기

Sequelize는 MySQL 작업을 쉽게 할 수 있도록 도와주는 라이브러리이다.  
Sequealize를 사용하기 위해서는 sequelize-cli를 `-g` 옵션으로 설치를 해준다.

```shell script
npm install sequelize mysql2
npm install -g sequelize-cli
sequelize init
```

