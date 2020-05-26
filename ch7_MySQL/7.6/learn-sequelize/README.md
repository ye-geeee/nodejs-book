# Chap 7. MySQL

## MySQL 시작하기

MySQL 실행을 위해 아래의 cmd를 사용한다.  
```shell script
mysql -h localhost -u root -p
```

## Sequelize 이용하기

Sequelize는 MySQL 작업을 쉽게 할 수 있도록 도와주는 라이브러리이다.  
Sequealize를 사용하기 위해서는 sequelize-cli를 `-g` 옵션으로 설치를 해준다.

``` shell script
npm install sequelize mysql2
npm install -g sequelize-cli
sequelize init
```

## MySQL 연결하기

아래와 같이 코드를 `app.js`에 추가해야 sequelize를 사용할 수 있다. 

``` js
const sequelize = require('./models').sequelize;

sequelize.sync();
```

MySQL에 정의한 테이블을 시퀄라이즈에서 동일하게 정의해준다.  
DB의 테이블명에 맞게 시퀄라이즈에서 모델을 정의하는데, 일반적으로 테이블은 복수형으로 모델은 단수형으로 작성한다.  
모델을 생성했다면 `./models/index.js`에 아래와 같이 연결한다.  

``` js
db.sequelize = sequelize;
db.Sequelize = Sequelize;

db.User = require('./user')(sequelzse, Sqequelize);
db.Comment = require('./comment')(sequelize, Sequelize);
```

DB에 대한 설정은 `config` 폴더에 정의한다.  
[config/config.json](./config/config.json)에는 각각 `development`와 `production`이 있는데, `development`는 `process.env.NODE_ENV`가 `development`일 때 적용되고, 실제 배포 환경을 위해서는 `production` 부분을 수정하면 된다. 

## 모델 관계 정의하기

모델 관계를 정의하는 부분은 [./model/index.js](./model/index.js)에 정의한다.
1:N, 1:1, N:M의 경우가 있다. `npm start`를 할 경우, 관련하여 시퀄라이즈가 DB에 쿼리를 날리는 부분을 콘솔을 통해 확인할 수 있다. 

``` js
// 1:N의 경우
db.User.hasMany(db.Comment, { foreignKey: 'commenter', sourceKey: 'id' });
db.Comment.belongsTo(db.User, { foreignKey: 'commenter', targetKey: 'id' });

// 1:1의 경우
db.User.hasOne(db.Info, {foreignKey: 'user_id', sourceKey: 'id'});
db.Info.belongsTo(db.User, {foreignKey: 'user_id', targetKey: 'id'});

// N:M의 경우
db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' });
db.Hashtag.belongsTo(db.Post, { through: 'PostHashtag' });
```

## 쿼리 작성하기

### Insert문

``` js
const { User } = require('../models');

User.create({
    name: 'zero',
    age: 24,
    married: false,
    comment: '자기소개1'
});
```

## Select문

``` js
// SELECT * FROM nodejs.users;
User.findAll({});

// SELECT * FROM nodejs.users LIMIT 1;
User.find({});

// SELECT name, married FROM nodejs.users;
User.findAll({
    attributes: ['name', 'married'],
});

// SELECT name, age FROM nodejs.users WHERE married = 1 AND age > 30;
const { User, Sequelize: { Op } }  = require('../models');
User.findAll({
    attributes: ['name', 'age'],
    where: {
        married: 1,
        age: { [Op.gt]: 30},
    },
});

// SELECT name, age FROM nodejs.users WHERE married = 0 OR age > 30;
const { User, Sequelize: { Op } }  = require('../models');
User.findAll({
    attributes: ['name', 'age'],
    where: {
        [Op.or]: [{ married: 0 }, { age: { [Op.gt]: 30} }],
    },
});

// SELECT name, age FROM nodejs.users ORDER BY age DESC;
User.findAll({
    attributes: ['name', 'age'],
    order: [['age', 'DESC']],
});
```
