## sequelize

---



### sequelize 이해하기

---



> Sequelize is a modern TypeScript and Node.js ORM for Postgres, MySQL, MariaDB, SQLite and SQL Server, and more. Featuring solid transaction support, relations, eager and lazy loading, read replication and more.



Sequelize는 SQL을 위한 Node.js ORM이다.



#### ORM

---

sequelize를 알아보기 전 우선 ORM에 대해 알아보자.

ORM은 데이터 조작과 데이터베이스에서 온 객체를 사용한 질의하는 것(querying)을 도와주는 Object Relational Mapper 이다.

(An ORM is simply an Object Relational Mapper that helps in data manipulation and querying by the use of objects from the database.)

ORM을 optimizes를 사용하여 SQL 쿼리의 재사용과 유지가 쉬워지고 DB와 객체 사이의 데이터 전환을 간단하게 해준다.

이런 ORM을 사용하기 위해, 우리는 첫번째로 DB를 선택하여야 한다.

그리고 ORM을 선택하는데 Node.js에서 SQL을 사용할 때는 주로 Sequelize를 사용한다.

그 후, DB 모델을 만든다. 

DB 모델을 만들며 정의된 구조는 테이블, 컬렉션 그리고 컬럼을 포함한다. 

이는 DB의 구조를 나타내며 확장성과 효율에서 이점을 제공해준다.

이렇게 모델까지 정의하면 우리는 DB와 ORM을 연결할 수 있다.



#### sequelize

---

위와 같은 ORM 중의 하나가 sequelize이다.

Sequelize는 Node.js의 모듈로 DB로 MySQL, Postgres를 사용할 때 사용하는 ORM이다.

조금 더 sequelize에 대해 알아보기 위해 sequelize를 간단히 구현한 아래 코드를 살펴보자.

```javascript
// instantiate sequelize
const Sequelize = require('sequelize');

// connect db
const connection = new Sequelize("db name", "username", "password");

// define article model
const Article = connection.define("article", {
    title: Sequelize.String,
    content: Sequelize.String
});
connection.sync();
```

먼저 sequelize 생성자 객체를 Sequelize라 정의하였다. 

이 생성자를 활용하여 DB를 정의하였다.

그 후 모델을 정의하였다.

위와 같은 작업은 비동기적으로 처리하면 오류가 생길 수 있어 동기적으로 처리하기 위해 `connection.sync()` 와 같이 `sync()` 함수를 사용하였다.

이렇게 준비가 완료되면 아래 코드와 같이 sequelize의 API를 사용하여 DB에 질의 할 수 있다.

```javascript
// query using sequelize API
Article.findById(5).then(function (article) {
    console.log(article);
})
```



우선 위의 코드를 통해 간단히 sequelize를 알아보았다.

이러한 sequelize의 장점은 다음과 같다.

- Sequelize는 우리가 더 적은 코드를 작성할 수 있게 해준다.
- 우리가 더 지속성 있는 코드를 작성하게 해준다.
- SQL 쿼리 작성을 피할 수 있게 해준다.
- Sequelize가 DB 엔진을 추상화 해준다.
- 이주(migration)에 좋은 툴이다.




<details>
<summary>참고</summary>
<a href=https://www.section.io/engineering-education/introduction-to-sequalize-orm-for-nodejs/>https://www.section.io/engineering-education/introduction-to-sequalize-orm-for-nodejs/</a> 
</details>


### sequelize 활용하기(1)

---



#### Connecting to a database

---

DB에 연결하기 위해서, Sequelize 인스턴스를 만들어야만 한다.

이는 Sequelize 생성자에 매개변수를 통해 값을 전달하거나 연결할 DB의 URI를 넣거는 방식으로 생성할 수 있다.

```javascript
const { Sequelize } = require('sequelize');

// Option 1: Passing a connection URI
const sequelize = new Sequelize('sqlite::memory:') // Example for sqlite
const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname') // Example for postgres

// Option 2: Passing parameters separately (other dialects)
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* one of 'mysql' | 'mariadb' | 'postgres' | 'mssql' */
});
```



#### Testing the connection

---

위에서 연결한 DB와 잘 연결되었는지 확인하기 위해서는 `.authenticate()` 메서드를 사용하여 확인할 수 있다.

```javascript
try {
  await sequelize.authenticate();
  console.log('Connection has been established successfully.');
} catch (error) {
  console.error('Unable to connect to the database:', error);
}
```



#### Closing the connection

---

만약 DB와 연결을 끊고 싶다면 `seqelize.close()` 를 통해 연결을 끊을 수 있다.



#### Promises and async/await

---

Sequelize에 의해 제공되는 대부분의 메서드들은 비동기적이다. 

그래서 이들은 Promises를 반환한다. 

그렇기에 우리는 Promise API (`then` , `catch` , `finally` )를 사용할 수 있다.

물론 `async` 와 `await` 역시 사용할 수 있다.



#### Model

---

Sequelize에서 model은 필수적이다.

model은 DB안의 테이블을 나타내는 추상이다.

Sequelize에서는 이것은 Model 클래스를 확장하는 클래스이다.



model은 Sequelize에게 DB안의 테이블 이름이 무엇이고 어떤 칼럼을 그것이 가지고 있는지와 같은 그것이 나타내는 엔터티는 무엇인지 말해주는 것들을 가지고 있다. 



> 엔터티란 “업무에 필요하고 유용한 정보를 저장하고 관리하기 위한 집합적인 것(Thing)”으로 설명할 수 있다.



Sequelize에서 model은 이름을 가지고 있는데 이 이름은 그것이 나타내는 DB안에 있는 테이블의 이름과 같을 필요는 없다.

대게 model은 단수형 이름을 가지고 테이블은 복수형 이름을 가진다. 



> model  -  `User`
>
> table -  `Users`



##### Model Definition

---

Sequelize에서 model은 크게 2가지 방법으로 정의된다.

- Calling `seqeulize.define(modelName, attributes, options)`
- Extending Model and calling `init(attributes, options)`



아래는 위의 2가지 방법을 활용하여 model 정의한 예제 코드이다.

```javascript
const { Sequelize, DataTypes } = require('sequelize');
const sequelize = new Sequelize('sqlite::memory:');

const User = sequelize.define('User', {
  // Model attributes are defined here
  firstName: {
    type: DataTypes.STRING,
    allowNull: false
  },
  lastName: {
    type: DataTypes.STRING
    // allowNull defaults to true
  }
}, {
  // Other model options go here
});

// `sequelize.define` also returns the model
console.log(User === sequelize.models.User); // true
```



```javascript
const { Sequelize, DataTypes, Model } = require('sequelize');
const sequelize = new Sequelize('sqlite::memory:');

class User extends Model {}

User.init({
  // Model attributes are defined here
  firstName: {
    type: DataTypes.STRING,
    allowNull: false
  },
  lastName: {
    type: DataTypes.STRING
    // allowNull defaults to true
  }
}, {
  // Other model options go here
  sequelize, // We need to pass the connection instance
  modelName: 'modelName' // We need to choose the model name
});

// the defined model is the class itself
console.log(User === sequelize.models.User); // true
```



```javascript
// 아래 코드는 model을 따로 구현할 경우 예제이다.

const { DataTypes, Sequelize } = require('sequelize');

module.exports = class User extends Sequelize.Model {
  // 클래스 메서드 init은 sequelize를 매개변수로 받는다.
  static init(sequelize) {
    // Sequelize.Model의 init 메서드를 활용하여 그 값을 반환 받는다.
    return super.init({
      email: {
        type: DataTypes.STRING(40),
        allowNull: true,
        unique: true,
      },
      password: {
        type: DataTypes.STRING(100),
        allowNull: true,
      }
    }, {
      sequelize,
      modelName: 'User',
      tableName: 'users'
    });
  }
};
```

`sequelize.define` 그리고 `Model.init` 는 내부적으로 두 접근 방법은 동일하다.

그렇기에 둘중 어느 것을 사용하여도 상관없다.



다만 한가지 주목할 것은 `Model.init` 을 사용할 때의 `option` 이다.

위의 예제의 `Model.init` 의 `option` 값을 살펴 보면 `seqeulize` 그리고 `modelName` 이 있다.

이는 `User` 가 sequelize에 의해 생성된 객체가 아니라 Sequelize 클래스의 Model 클래스 메서드를 활용한 것이기 때문에 `seqeulize` 를 `option` 을 통해 넣어준 것 같다. 

그리고 `modelName` 역시 동일한 이유로 넣어준 것이라 생각한다.

아래 코드는 위의 생각을 뒷받침할 `node_modules/sequelize/lib/model.js` 속의 코드이다.

```javascript
  static init(attributes, options = {}) {
    var _a, _b;
    if (!options.sequelize) {
      throw new Error("No Sequelize instance passed");
    }
    this.sequelize = options.sequelize; // `sequelize`을 option 값에 넣는 이유
    const globalOptions = this.sequelize.options;
    options = Utils.merge(_.cloneDeep(globalOptions.define), options);
    if (!options.modelName) {
      options.modelName = this.name;
    }
    // `modelName`을 option 값에 넣는 이유
    options = Utils.merge({
      name: {
        plural: Utils.pluralize(options.modelName),
        singular: Utils.singularize(options.modelName)
      },
      indexes: [],
      omitNull: globalOptions.omitNull,
      schema: globalOptions.schema
    }, options);
    
   // ....
```



이렇게 model이 정의되고난 이후, 그것은 `sequelize.models.modelName` 혹은 이를 담은 변수  `modelName` 과 같은 형태로 사용될 수 있다.



##### Table name inference

---

위의 model을 정의할 때, 2가지 메서드 모두 modelName은 정의하였지만 tableName은 정의하지 않았다. 



기본적으로 tableName이 주어지지 않으면 Sequelize는 자동적으로 modelName을 복수화하여 그것을 tableName으로 활용한다.

이 복수화는 `inflection` 이라는 라이브러리를 통해 진행되어 `person -> people` 과 같은 것도 복수화가 진행된다.



tableName을 자동이 아니라 직접 설정하고 싶다면 `option` 값에 `tableName : 'tableName'`  을 넣어주어 직접 설정하면 된다.



만약 tableName을 modelName과 동일하게 하고 싶다면 `option` 값에 `freezeTable : true` 을 넣어주면 된다.

이를 전체 모델에 적용하고 싶으면 아래 코드와 같이 sequelize를 생성하면 된다.

``` javascript
const sequelize = new Sequelize('sqlite::memory:', {
  define: {
    freezeTableName: true
  }
});
```



##### Model synchronization

---

model을 정의할 때 Sequelize에게 DB안의 테이블에 관한 정보를 주었다.

(`.define` 그리고 `init` 메서드를 통해서 model을 정의하였다.)

하지만 테이블이 DB안에 존재하지 않으면?

만약 존재하더라도 칼럼이 다르거나, 적거나 혹은 다른 다른 것이 있다면? 



위와 같은 이유 때문에 model synchronization이 필요하다.

model은 `model.sync(options)` 와 같은 명령을 통해 DB와 synchronize 될 수 있다. 

위의 명령을 통해, Sequelize는 자동으로 DB에 SQL query를 보낸다.

이를 통한 변화는 DB의 테이블에서 일어나지 Javascript의 model에서 일어나지 않는다.



우리가 DB를 사용할 때 model을 하나만 사용하지는 않을 것이다. 

모든 model을 한번에 synchronizing 하기 위해서는 아래와 같은 코드를 사용하면 된다.

```javascript
await sequelize.sync({ force: true });
console.log("All models were synchronized successfully.");
```



즉, synchronizing을 하기 위해서는 사전에 model이 정의되어 있어야 한다.

이를 확인하기 위해서 `console.log()` 를 활용해 보았다.



본인이 공부하고 있는 폴더의 구조를 간단히 하면 다음과 같다.

```
app_practice
	sequelize
		models
			index.js
	app.js
```

```javascript
// app.js

// serverStart  
dotenv.config();
console.log("sequelize is faster!");
sequelize.sync();
passportConfig();
```

```javascript
const db = {};
db.User = User;
db.sequelize = sequelize; 

console.log("init is faster!");
User.init(sequelize);

module.exports = db;
```

![sequelize](C:\Users\User\Desktop\Study\Nodong\Npm\image\sequelize.JPG)

위의 사진은 서버를 시작한 후 `console` 에 나온 결과이다.

model을 정의하는 `.init()` 이 먼저 수행되고 

`.sync()` 가 수행된다.



##### Dropping tables

---

model과 연관된 테이블을 삭제하기 위해서는 아래와 같은 코드를 

```javascript
await User.drop();
console.log("User table dropped!");
```

모든 테이블을 삭제하기 위해서는 아래와 같은 코드를 사용하면 된다.

```javascript
await sequelize.drop();
console.log("All tables dropped!");
```



##### Timestamps

---

기본적으로 Sequelize는 자동적으로 `createdAt` 그리고 `updateAt` 이라는 필드를 모든 model에 `DataTypes.DATE` 를 활용하여 추가한다. 

그 필드들은 자동적으로 관리가 된다.

`createdAt` 필드는 생성된 순간을 나타내는 타임스탬프 값을 가지고 있고 `updateAt` 은 가장 최근 업데이트된 타임스탬프 값을 가지고 있다.



아래는 timestamps를 설정하는 예제 코드이다.

```javascript
sequelize.define('User', {
  // ... (attributes)
}, {
  timestamps: false
});
```

```javascript
class Foo extends Model {}
Foo.init({ /* attributes */ }, {
  sequelize,

  // don't forget to enable timestamps!
  timestamps: true,

  // I don't want createdAt
  createdAt: false,

  // I want updatedAt to actually be called updateTimestamp
  updatedAt: 'updateTimestamp'
});
```



##### Coulmn declaration shorthand syntax

---



```javascript
// This:
sequelize.define('User', {
  name: {
    type: DataTypes.STRING
  }
});

// Can be simplified to:
sequelize.define('User', { name: DataTypes.STRING });
```



##### Default Values

---

Sequelize는 칼럼의 기본값을 `Null` 으로 가지고 있다.

이 값은 `defaultValue` 라는 것을 통해 바꿀 수 있다.



이를 활용한 코드는 아래와 같다.

```javascript
sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    defaultValue: "John Doe"
  }
});
```

```javascript
sequelize.define('Foo', {
  bar: {
    type: DataTypes.DATETIME,
    defaultValue: DataTypes.NOW
    // This way, the current date/time will be used to populate this column (at the moment of insertion)
  }
});
```



##### Data Types

---

model에 정의된 모든 칼럼은 data type을 가지고 있어야 한다.

Sequelize는 많은 내장 data type을 제공한다.

내장 data type에 접근하려면 `DataTypes` 를 다음과 같이 import 하면 된다.

```javascript
const { DataTypes } = require("sequelize"); // Import the built-in data types
```



**Strings, Boolean, Numbers, Dates, UUIDs** 등에 관한 data type이 정의되어 있다. 



자세한 것은 다음을 참고 하자.

[Data Types]: https://sequelize.org/docs/v6/core-concepts/model-basics/#data-types	"Data Types"



##### Coulmn Options

---

위에서 컬럼을 정의할 때 `type` 뿐만 아니라 `allowNull` 그리고 `defaultValue` 를 `option` 에 사용하여 보았다.

아직 다른 다양한 `option` 들이 남아 있다.

이는 아래와 같다.

```javascript
const { Model, DataTypes, Deferrable } = require("sequelize");

class Foo extends Model {}
Foo.init({
  // instantiating will automatically set the flag to true if not set
  flag: { type: DataTypes.BOOLEAN, allowNull: false, defaultValue: true },

  // default values for dates => current time
  myDate: { type: DataTypes.DATE, defaultValue: DataTypes.NOW },

  // setting allowNull to false will add NOT NULL to the column, which means an error will be
  // thrown from the DB when the query is executed if the column is null. If you want to check that a value
  // is not null before querying the DB, look at the validations section below.
  title: { type: DataTypes.STRING, allowNull: false },

  // Creating two objects with the same value will throw an error. The unique property can be either a
  // boolean, or a string. If you provide the same string for multiple columns, they will form a
  // composite unique key.
  uniqueOne: { type: DataTypes.STRING,  unique: 'compositeIndex' },
  uniqueTwo: { type: DataTypes.INTEGER, unique: 'compositeIndex' },

  // The unique property is simply a shorthand to create a unique constraint.
  someUnique: { type: DataTypes.STRING, unique: true },

  // Go on reading for further information about primary keys
  identifier: { type: DataTypes.STRING, primaryKey: true },

  // autoIncrement can be used to create auto_incrementing integer columns
  incrementMe: { type: DataTypes.INTEGER, autoIncrement: true },

  // You can specify a custom column name via the 'field' attribute:
  fieldWithUnderscores: { type: DataTypes.STRING, field: 'field_with_underscores' },

  // It is possible to create foreign keys:
  bar_id: {
    type: DataTypes.INTEGER,

    references: {
      // This is a reference to another model
      model: Bar,

      // This is the column name of the referenced model
      key: 'id',

      // With PostgreSQL, it is optionally possible to declare when to check the foreign key constraint, passing the Deferrable type.
      deferrable: Deferrable.INITIALLY_IMMEDIATE
      // Options:
      // - `Deferrable.INITIALLY_IMMEDIATE` - Immediately check the foreign key constraints
      // - `Deferrable.INITIALLY_DEFERRED` - Defer all foreign key constraint check to the end of a transaction
      // - `Deferrable.NOT` - Don't defer the checks at all (default) - This won't allow you to dynamically change the rule in a transaction
    }
  },

  // Comments can only be added to columns in MySQL, MariaDB, PostgreSQL and MSSQL
  commentMe: {
    type: DataTypes.INTEGER,
    comment: 'This is a column name that has a comment'
  }
}, {
  sequelize,
  modelName: 'foo',

  // Using `unique: true` in an attribute above is exactly the same as creating the index in the model's options:
  indexes: [{ unique: true, fields: ['someUnique'] }]
});
```



#### Model Instance

---



##### Creating an instance

---

model은 클래스로 `new` 를 사용하여 직접적으로 인스턴스를 만드는 것이 아니라 `build` 를 사용하여야 한다.

```javascript
const jane = User.build({ name: "Jane" });
console.log(jane instanceof User); // true
console.log(jane.name); // "Jane"
```



하지만 위의 코드는 DB와 전혀 소통하지 않는다.

이는 `build` 메서드가 DB와 매칭할 수 있는 데어터를 나타내는 객체를 만들기 때문이다.

(This is because the [`build`](https://sequelize.org/api/v6/class/src/model.js~Model.html#static-method-build) method only creates an object that *represents* data that *can* be mapped to a database.)

이 인스턴스를 진짜 DB에 저장하기 위해서는 `save` 메서드를 사용하여야 한다.

```javascript
await jane.save();
console.log('Jane was saved to the database!');
```



위의 `build` 와 `save` 메서드를 합한 `create` 메서드를 Sequelize는 제공한다.

```javascript
const jane = await User.create({ name: "Jane" });
// Jane exists in the database now!
console.log(jane instanceof User); // true
console.log(jane.name); // "Jane"
```



##### Updating an instance

---

인스턴스의 필드 값을 바꾼다면, `save` 를 다시한번 사용하여야 한다.

```javascript
const jane = await User.create({ name: "Jane" });
console.log(jane.name); // "Jane"
jane.name = "Ada";
// the name is still "Jane" in the database
await jane.save();
// Now the name was updated to "Ada" in the database!
```

이때 `save` 는 그 인스턴스에서의 값의 변화 전체를 업데이트한다. 

만약 특정한 필드만 업데이트하고 싶다면 `update` 를 사용하면 된다.

```javascript
const jane = await User.create({ name: "Jane" });
jane.favoriteColor = "blue"
await jane.update({ name: "Ada" })
// The database now has "Ada" for name, but still has the default "green" for favorite color
await jane.save()
// Now the database has "Ada" for name and "blue" for favorite color
```

 여러 필드 값을 바꾸고 싶다면, `set` 메서드를 사용하면 된다.

```javascript
const jane = await User.create({ name: "Jane" });

jane.set({
  name: "Ada",
  favoriteColor: "blue"
});
// As above, the database still has "Jane" and "green"
await jane.save();
// The database now has "Ada" and "blue" for name and favorite color
```



##### Deleting an instance

---

`destroy` 메서드를 통해서 인스턴스를 삭제할 수 있다.

```javascript
const jane = await User.create({ name: "Jane" });
console.log(jane.name); // "Jane"
await jane.destroy();
// Now this entry was removed from the database
```



##### Reloading an instance

---

`reload` 메서드를 통해서 데이터베이스로부터 인스턴스를 다시 불러올 수 있다.

```javascript
const jane = await User.create({ name: "Jane" });
console.log(jane.name); // "Jane"
jane.name = "Ada";
// the name is still "Jane" in the database
await jane.reload();
console.log(jane.name); // "Jane"
```



##### Incrementing and decrementing integer values

---

동시성(concurrency)에 관한 이슈 없이 인스턴스의 값을 정렬하기 위해, Sequelize는 `increment` 그리고 `decrement` 메서드를 제공한다.

```javascript
const jane = await User.create({ name: "Jane", age: 100 });
const incrementResult = await jane.increment('age', { by: 2 });
// Note: to increment by 1 you can omit the `by` option and just do `user.increment('age')`

// In PostgreSQL, `incrementResult` will be the updated user, unless the option
// `{ returning: false }` was set (and then it will be undefined).

// In other dialects, `incrementResult` will be undefined. If you need the updated instance, you will have to call `user.reload()`.
```

```javascript
const jane = await User.create({ name: "Jane", age: 100, cash: 5000 });
await jane.increment({
  'age': 2,
  'cash': 100
});

// If the values are incremented by the same amount, you can use this other syntax as well:
await jane.increment(['age', 'cash'], { by: 2 });
```



#### Model Querying - Basics

---

Sequelize는 DB의 데이터를 질의(querying)하기 위한 다양한 메서드를 제공한다.

아래는 특히 CRUD 쿼리를 의주로 알아보려 한다.



##### Simple INSERT queries

---

```javascript
// Create a new user
const jane = await User.create({ firstName: "Jane", lastName: "Doe" });
console.log("Jane's auto-generated ID:", jane.id);
```

`Model.create()` 메서드로 앞서 본 메서드이다.



```javascript
const user = await User.create({
  username: 'alice123',
  isAdmin: true
}, { fields: ['username'] });
// let's assume the default of isAdmin is false
console.log(user.username); // 'alice123'
console.log(user.isAdmin); // false
```

이 메서드는 유저에 의해 채워지는 폼에 기반한 DB를 만들 때 특히 유용하다.

예를 들어, `create` 를 사용하면 `User` 모델이 username은 작성할 수 있지만 admin flag는 작성할 수 없다.

위의 코드에서 볼 수 있듯 

`isAdmin` 의 기본값이 false라면 `create` 를 통해 true라고 인스턴스를 만들더라도 false가 유지된다. 



##### Simple SELECT queries

---

`findAll` 메서드를 통해 DB 테이블 전체를 읽을 수 있다.

```javascript
// Find all users
const users = await User.findAll();
console.log(users.every(user => user instanceof User)); // true
console.log("All users:", JSON.stringify(users, null, 2));
```

```
SELECT * FROM ...
```



##### Specifying attributes for SELECT queries

---

위에서는 테이블 전체를 읽어 왔는데 특정 속성만 읽어오려면 `attributes` 옵션을 사용하면 된다.

```javascript
Model.findAll({
  attributes: ['foo', 'bar']
});
```

```
SELECT foo, bar FROM ...
```



이때 불러오는 속성의 이름을 다르게 해서 읽어오려면 배열(nested array)를 활용하면 된다.

```javascript
Model.findAll({
  attributes: ['foo', ['bar', 'baz'], 'qux']
});
```

```
SELECT foo, bar AS baz, qux FROM ...
```



집계함수(aggregation)을 사용하여 속성을 가져오려면 `sequelize.fn` 을 사용하면 된다.

`sequelize.fn` 은 `function fn(fn: string, ...args: unknown[]): Fn;`  와 같이 정의되어 있어 문자열로 sql에서의 함수명을 받고 이후 그것을 적용할 칼럼을 받는다.

`sequelize.fn` 은 아래서 다시 알아보도록 하자.

```javascript
Model.findAll({
  attributes: [
    'foo',
    [sequelize.fn('COUNT', sequelize.col('hats')), 'n_hats'],
    'bar'
  ]
});
```

```
SELECT foo, COUNT(hats) AS n_hats, bar FROM ...
```

집계함수를 사용할 때는 model에서 그것에 접근할 수 있는 별명(alias)를 설정하여야 한다.

이는 결과 값의 속성명으로 사용된다.



속성 값을 불러올 때 불러올 속성 값을 설정하는 것이 아니라 불러오지 않을 속성 값을 설정하는 방법도 있다.

```javascript
Model.findAll({
  attributes: { exclude: ['baz'] }
});
```

```
-- Assuming all columns are 'id', 'foo', 'bar', 'baz' and 'qux'
SELECT id, foo, bar, qux FROM ...
```

이때 `attributes` 의 값으로 다른 것과 같이 배열이 들어가는 것이 아니라 객체가 들어간다는 것에 주목하자. 

객체를 통해 옵션을 전달해서 해당 속성을 제외하는 것이다.



##### Applying WHERE clauses

---

`where` 옵션은 질의를 분류(filter)하기 위해 사용된다.

이는 `Op` 에 의해 이루어 진다.



`Op.eq` 

```javascript
const { Op } = require("sequelize");
Post.findAll({
  where: {
    authorId: {
      [Op.eq]: 2
    }
  }
});
// SELECT * FROM post WHERE authorId = 2;
```



`Op.and` 

```javascript
const { Op } = require("sequelize");
Post.findAll({
  where: {
    [Op.and]: [
      { authorId: 12 },
      { status: 'active' }
    ]
  }
});
// SELECT * FROM post WHERE authorId = 12 AND status = 'active';
```



`Op.or`

```javascript
const { Op } = require("sequelize");
Post.findAll({
  where: {
    [Op.or]: [
      { authorId: 12 },
      { authorId: 13 }
    ]
  }
});
// SELECT * FROM post WHERE authorId = 12 OR authorId = 13;
```

```javascript
const { Op } = require("sequelize");
Post.destroy({
  where: {
    authorId: {
      [Op.or]: [12, 13]
    }
  }
});
// DELETE FROM post WHERE authorId = 12 OR authorId = 13;
```



##### Operators

---

```javascript
const { Op } = require("sequelize");
Post.findAll({
  where: {
    [Op.and]: [{ a: 5 }, { b: 6 }],            // (a = 5) AND (b = 6)
    [Op.or]: [{ a: 5 }, { b: 6 }],             // (a = 5) OR (b = 6)
    someAttribute: {
      // Basics
      [Op.eq]: 3,                              // = 3
      [Op.ne]: 20,                             // != 20
      [Op.is]: null,                           // IS NULL
      [Op.not]: true,                          // IS NOT TRUE
      [Op.or]: [5, 6],                         // (someAttribute = 5) OR (someAttribute = 6)

      // Using dialect specific column identifiers (PG in the following example):
      [Op.col]: 'user.organization_id',        // = "user"."organization_id"

      // Number comparisons
      [Op.gt]: 6,                              // > 6
      [Op.gte]: 6,                             // >= 6
      [Op.lt]: 10,                             // < 10
      [Op.lte]: 10,                            // <= 10
      [Op.between]: [6, 10],                   // BETWEEN 6 AND 10
      [Op.notBetween]: [11, 15],               // NOT BETWEEN 11 AND 15

      // Other operators

      [Op.all]: sequelize.literal('SELECT 1'), // > ALL (SELECT 1)

      [Op.in]: [1, 2],                         // IN [1, 2]
      [Op.notIn]: [1, 2],                      // NOT IN [1, 2]

      [Op.like]: '%hat',                       // LIKE '%hat'
      [Op.notLike]: '%hat',                    // NOT LIKE '%hat'
      [Op.startsWith]: 'hat',                  // LIKE 'hat%'
      [Op.endsWith]: 'hat',                    // LIKE '%hat'
      [Op.substring]: 'hat',                   // LIKE '%hat%'
      [Op.iLike]: '%hat',                      // ILIKE '%hat' (case insensitive) (PG only)
      [Op.notILike]: '%hat',                   // NOT ILIKE '%hat'  (PG only)
      [Op.regexp]: '^[h|a|t]',                 // REGEXP/~ '^[h|a|t]' (MySQL/PG only)
      [Op.notRegexp]: '^[h|a|t]',              // NOT REGEXP/!~ '^[h|a|t]' (MySQL/PG only)
      [Op.iRegexp]: '^[h|a|t]',                // ~* '^[h|a|t]' (PG only)
      [Op.notIRegexp]: '^[h|a|t]',             // !~* '^[h|a|t]' (PG only)

      [Op.any]: [2, 3],                        // ANY (ARRAY[2, 3]::INTEGER[]) (PG only)
      [Op.match]: Sequelize.fn('to_tsquery', 'fat & rat') // match text search for strings 'fat' and 'rat' (PG only)

      // In Postgres, Op.like/Op.iLike/Op.notLike can be combined to Op.any:
      [Op.like]: { [Op.any]: ['cat', 'hat'] }  // LIKE ANY (ARRAY['cat', 'hat'])

      // There are more postgres-only range operators, see below
    }
  }
});
```

sql에서 사용하는 많은 조건들을 `Operator` 을 통해 사용할 수 있다.

더 자세한 사용법은 다음 링크를 참고하자.

[Operators]: https://sequelize.org/docs/v6/core-concepts/model-querying-basics/#operators



##### Simple UPDATE queries

---

```javascript
// Change everyone without a last name to "Doe"
await User.update({ lastName: "Doe" }, {
  where: {
    lastName: null
  }
});
```



##### Simple DELETE queries

---

```javascript
// Delete everyone named "Jane"
await User.destroy({
  where: {
    firstName: "Jane"
  }
});
```



##### Creating in bulk

---

앞서 새로운 데이터를 `create` 할 때 하나씩 하였다. 

하지만 `Model.bulkCreate` 를 사용하면 여러 데이터를 한번에 `create` 할 수 있다.

```javascript
const captains = await Captain.bulkCreate([
  { name: 'Jack Sparrow' },
  { name: 'Davy Jones' }
]);
console.log(captains.length); // 2
console.log(captains[0] instanceof Captain); // true
console.log(captains[0].name); // 'Jack Sparrow'
console.log(captains[0].id); // 1 // (or another auto-generated value)
```



`bulkCreate` 를 사용할 때 주의점은 `bulkCreate` 의 기본 값이 만들 각 객체의 유효성(validate) 검사를 하지 않는다는 것이다.

`bulkCreate` 가 유효성 검사를 하게 하려면 `validate : true` 라는 옵션을 제공하여야 한다.

```javascript
const Foo = sequelize.define('foo', {
  bar: {
    type: DataTypes.TEXT,
    validate: {
      len: [4, 6]
    }
  }
});

// This will not throw an error, both instances will be created
await Foo.bulkCreate([
  { name: 'abc123' },
  { name: 'name too long' }
]);

// This will throw an error, nothing will be created
await Foo.bulkCreate([
  { name: 'abc123' },
  { name: 'name too long' }
], { validate: true });
```

 

##### Ordering and Grouping

---

Sequelize는 sql의 `ORDER BY` 그리고 `GROUP BY` 에 해당하는 `order` 그리고 `group` 을 생성한다.



###### Ordering

`order` 옵션은 쿼리나 sequelize 메서드로 정렬할 아이템의 배열을 받는다.

이 아이템은 `[column, direction]` 형태의 배열이다. 

```javascript
Subtask.findAll({
  order: [
    // Will escape title and validate DESC against a list of valid direction parameters
    ['title', 'DESC'],

    // Will order by max(age)
    sequelize.fn('max', sequelize.col('age')),

    // Will order by max(age) DESC
    [sequelize.fn('max', sequelize.col('age')), 'DESC'],

    // Will order by  otherfunction(`col1`, 12, 'lalala') DESC
    [sequelize.fn('otherfunction', sequelize.col('col1'), 12, 'lalala'), 'DESC'],

    // Will order an associated model's createdAt using the model name as the association's name.
    [Task, 'createdAt', 'DESC'],

    // Will order through an associated model's createdAt using the model names as the associations' names.
    [Task, Project, 'createdAt', 'DESC'],

    // Will order by an associated model's createdAt using the name of the association.
    ['Task', 'createdAt', 'DESC'],

    // Will order by a nested associated model's createdAt using the names of the associations.
    ['Task', 'Project', 'createdAt', 'DESC'],

    // Will order by an associated model's createdAt using an association object. (preferred method)
    [Subtask.associations.Task, 'createdAt', 'DESC'],

    // Will order by a nested associated model's createdAt using association objects. (preferred method)
    [Subtask.associations.Task, Task.associations.Project, 'createdAt', 'DESC'],

    // Will order by an associated model's createdAt using a simple association object.
    [{model: Task, as: 'Task'}, 'createdAt', 'DESC'],

    // Will order by a nested associated model's createdAt simple association objects.
    [{model: Task, as: 'Task'}, {model: Project, as: 'Project'}, 'createdAt', 'DESC']
  ],

  // Will order by max age descending
  order: sequelize.literal('max(age) DESC'),

  // Will order by max age ascending assuming ascending is the default order when direction is omitted
  order: sequelize.fn('max', sequelize.col('age')),

  // Will order by age ascending assuming ascending is the default order when direction is omitted
  order: sequelize.col('age'),

  // Will order randomly based on the dialect (instead of fn('RAND') or fn('RANDOM'))
  order: sequelize.random()
});

Foo.findOne({
  order: [
    // will return `name`
    ['name'],
    // will return `username` DESC
    ['username', 'DESC'],
    // will return max(`age`)
    sequelize.fn('max', sequelize.col('age')),
    // will return max(`age`) DESC
    [sequelize.fn('max', sequelize.col('age')), 'DESC'],
    // will return otherfunction(`col1`, 12, 'lalala') DESC
    [sequelize.fn('otherfunction', sequelize.col('col1'), 12, 'lalala'), 'DESC'],
    // will return otherfunction(awesomefunction(`col`)) DESC, This nesting is potentially infinite!
    [sequelize.fn('otherfunction', sequelize.fn('awesomefunction', sequelize.col('col'))), 'DESC']
  ]
});
```



###### Grouping

grouping의 문법은 ordering의 문법과 같다. 

다만 배열 마지막의 방향에 관한 것은 받지 않는다.

```javascript
Project.findAll({ group: 'name' });
// yields 'GROUP BY name'
```



##### Utility methods

---

`count`,  `max`,  `min`,  `sum`, `increment`, `decrement`  와 같은 메서드가 있다.

이는 위에서 알아보았던 것을 간단히 한 메서드이다.



##### Sequelize.fn()

---

`Sequelize.fn()`을 사용하면 sql 함수를 쉽게 사용할 수 있다.

```typescript
function fn(fn: string, ...args: unknown[]): Fn;
```

첫 번째 매개변수로 함수명을, 두 번째 매개변수 부터는 대상을 지정한다.

즉, sql에서 사용하는 함수명을 알고 있다면 `Squelize.fn()` 을 사용할 수 있는 것이다.



##### Sequelize.literal()

---

서술적 sql 함수는 `Sequelize.fn()` 으로 해결할 수 없다고 한다.

IF나 CASE의 경우에는 컬럼이나 상수 뿐만 아니라, >=, <=, >, <, IN 등의 문자가 들어가서 fn()으로 처리하기 까다롭다 한다.

이러한 상황에서 `Sequelize.literal()` 을 사용할 수 있다.

```javascript
const completeMail = Sequelize.literal(`
    if(user.is_local IS NULL, CONCAT(user.email, ${domain}), user.email)
`);

await this.model.findAndCountAll({
    attributes: ["id", [completeMail, "email"]],
    where: {
        id: user.id
    }
});
```



### sequelize 활용하기(2)

---



#### Model Querying - Finders

---

Finder 메서드는 `select` 커리를 만드는 메서드이다.

기본적으로 finder 메서드는 모두 모델의 인스턴스이다.

이것은 DB가 결과를 반환한 후에 Sequelize가 자동적으로 모든 것을 적절한 인스턴스 객체로 감싼다는 것을 뜻한다.

그렇기에 너무 많은 결과가 있으면, 이 감싸는 것은 효율적이지 못하게 된다.

이러한 과정을 없애고 순수한 결과를 받기 위해서는 `{ raw : true }` 를 finder 메서드의 옵션에 추가하면 된다.



##### findAll

---

`findAll` 은 기본적은 `SELECT` 쿼리를 만든다.



##### findByPk

---

`findByPk` 는 primary key를 사용하여 테이블에서 하나의 값을 가져오는 메서드이다.



```js
const project = await Project.findByPk(123);
if (project === null) {
  console.log('Not found!');
} else {
  console.log(project instanceof Project); // true
  // Its primary key is 123
}
```



##### findOne

---

`findOne` 은 처음으로 찾아지는 값을 가져오는 메서드이다.



```js
const project = await Project.findOne({ where: { title: 'My Title' } });
if (project === null) {
  console.log('Not found!');
} else {
  console.log(project instanceof Project); // true
  console.log(project.title); // 'My Title'
}
```



##### findOrCreate

---

`findOrCreate` 는 찾을 수 없는 값을 가진 경우 값을 만들고 찾을 수 있으면 그 값을 리턴해 준다.

그래서 반환되는 인스턴스는 boolean을 통해 이 인스턴스가 만들어진 것인지 아님 이미 존재한 것인지 알려준다.



`where` 옵션은 값을 찾는 옵션이다.

`default` 옵션의 경우 아무것도 찾아지지 않았을 때 만들어져야할 값을 정의하는 옵션이다.



다음은 이에 대한 예제 코드이다.

```js
const [user, created] = await User.findOrCreate({
  where: { username: 'sdepold' },
  defaults: {
    job: 'Technical Lead JavaScript'
  }
});
console.log(user.username); // 'sdepold'
console.log(user.job); // This may or may not be 'Technical Lead JavaScript'
console.log(created); // The boolean indicating whether this instance was just created
if (created) {
  console.log(user.job); // This will certainly be 'Technical Lead JavaScript'
}
```



##### findAndCountAll

---

`findAndCountAll` 메서드는 `findAll` 과 `count` 를 합친 편한 메서드이다.

이는 pagination과 연관된 쿼리를 다루는데 유용하다.

이는 `limit` 그리고 `offset` 을 활용하여 데이터를 검색할 때도 그것의 총 기록 수를 알려준다.



`group`  옵션이 없다면, `findAndCountAll` 메서드는 다음의 두가지 요소와 함께 객체를 반환한다.

- `count`  : 쿼리문에 맞는 기록의 총 수를 말한다.
- `rows` : 존재하는 기록의 총 수를 말한다.



`group`  옵션이 있다면, `findAndCountAll` 메서드는 다음의 두가지 요소와 함께 객체를 반환한다.

- `count`  : 각 그룹의 수 그리고 속성 수를 포함한다. 
- `rows` : 존재하는 기록의 총 수를 말한다.



```js
const { count, rows } = await Project.findAndCountAll({
  where: {
    title: {
      [Op.like]: 'foo%'
    }
  },
  offset: 10,
  limit: 2
});
console.log(count);
console.log(rows);
```



#### Getters, Setters & Virtuals

---

Sequelize는 우리의 모델 속성을 위한 getters 그리고 setter을 정의할 수 있다.



Sequelize는 또한 virtual attirbutes라고 불리는 것을 명시할 수 있도록 한다.

이것은 Sequelize Model에는 존재하지만 SQL table에는 없는 속성이다. 



##### Getters

---

getter은 `get()` 으로 모델을 정의할 때 한 칼럼을 속에 정의된 함수이다.



다음은 이에 대한 예제이다.

```js
const User = sequelize.define('user', {
  // Let's say we wanted to see every username in uppercase, even
  // though they are not necessarily uppercase in the database itself
  username: {
    type: DataTypes.STRING,
    get() {
      const rawValue = this.getDataValue('username');
      return rawValue ? rawValue.toUpperCase() : null;
    }
  }
});
```

```js
const user = User.build({ username: 'SuperUser123' });
console.log(user.username); // 'SUPERUSER123' 
console.log(user.getDataValue('username')); // 'SuperUser123'
```

`user.username` 은 get에서 설정한대로 대문자로 나오는 것을 볼 수 있다.

하지만 이는 우리가 보기에 대문자로 나오는 것이지 DB에 저장된 값은 `user.getDataValue` 를 통해 볼 수 있는 값이다.



##### Setters

---

setter은 `set()` 으로 모델을 정의할 때 한 칼럼을 속에 정의된 함수이다.



다음은 이에 대한 예제이다.

```js
const User = sequelize.define('user', {
  username: DataTypes.STRING,
  password: {
    type: DataTypes.STRING,
    set(value) {
      // Storing passwords in plaintext in the database is terrible.
      // Hashing the value with an appropriate cryptographic hash function is better.
      this.setDataValue('password', hash(value));
    }
  }
});
```

```js
const user = User.build({ username: 'someone', password: 'NotSo§tr0ngP4$SW0RD!' });
console.log(user.password); // '7cfc84b8ea898bb72462e78b4643cfccd77e9f05678ec2ce78754147ba947acc'
console.log(user.getDataValue('password')); // '7cfc84b8ea898bb72462e78b4643cfccd77e9f05678ec2ce78754147ba947acc'
```

Sequelize는 데이터를 DB로 보내기 전에 setter로 자동적으로 보낸다.



##### Combinbing getters and setters

---

getters 그리고 setters는 같은 필드에 정의될 수 있다.



다음은 이에 대한 예제 코드이다.

이는 post의 content의 크기가 클 경우를 대비하여 `set()` 을 통해 압축하고 `get()` 을 통해 압축을 풀어 보여주는  코드이다.

```js
const { gzipSync, gunzipSync } = require('zlib');

const Post = sequelize.define('post', {
  content: {
    type: DataTypes.TEXT,
    get() {
      const storedValue = this.getDataValue('content');
      const gzippedBuffer = Buffer.from(storedValue, 'base64');
      const unzippedBuffer = gunzipSync(gzippedBuffer);
      return unzippedBuffer.toString();
    },
    set(value) {
      const gzippedBuffer = gzipSync(value);
      this.setDataValue('content', gzippedBuffer.toString('base64'));
    }
  }
});
```

```js
const post = await Post.create({ content: 'Hello everyone!' });

console.log(post.content); // 'Hello everyone!'
// Everything is happening under the hood, so we can even forget that the
// content is actually being stored as a gzipped base64 string!

// However, if we are really curious, we can get the 'raw' data...
console.log(post.getDataValue('content'));
// Output: 'H4sIAAAAAAAACvNIzcnJV0gtSy2qzM9LVQQAUuk9jQ8AAAA='
```



##### Virtual fields

---

Virtual fields는 Sequelize가 hood 아래 작성한 field이다. 

하지만 이는 DB에 실제로 존재하지는 않는다.



다음은 이에 대한 예제 코드이다.

이는 `firstName` 그리고 `lastName` 을 가지고 있는 User가 있고 여기에 getter와 setter을 통해 `fullName` 이라는 Virtual fields를 만드는 것을 구현한 코드이다.

```js
const { DataTypes } = require("sequelize");

const User = sequelize.define('user', {
  firstName: DataTypes.TEXT,
  lastName: DataTypes.TEXT,
  fullName: {
    type: DataTypes.VIRTUAL,
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    set(value) {
      throw new Error('Do not try to set the `fullName` value!');
    }
  }
});
```

```js
const user = await User.create({ firstName: 'John', lastName: 'Doe' });
console.log(user.fullName); // 'John Doe'
```

`fullName` 은 DB에는 존재하지 않지만 우리는 사용할 수 있다.



#### Validations & Constraints

---

validation과 constraint에 대해 알아보기 위해 다음과 같이 모델을 준비하자.



```js
const { Sequelize, Op, Model, DataTypes } = require("sequelize");
const sequelize = new Sequelize("sqlite::memory:");

const User = sequelize.define("user", {
  username: {
    type: DataTypes.TEXT,
    allowNull: false,
    unique: true
  },
  hashedPassword: {
    type: DataTypes.STRING(64),
    validate: {
      is: /^[0-9a-f]{64}$/i
    }
  }
});

(async () => {
  await sequelize.sync({ force: true });
  // Code here
})();
```



##### Difference between Validations and Constraints

---

Validation은 Sequelize 레벨에서 순수 JavaScript로 수행되는 확인작업이다.

Sequelize에 의해 제공되는 내장 validator은 간편하다.

만약 인증이 실패하면, 어떤 SQL 쿼리도 DB에 전혀 보내지지 않는다.



반면 constraint는 SQL 레벨에서 정의된 확인작업이다.

가장 대표적인 예시가 Unique Constraint이다.



##### Constraint

---



###### Unique Constraint

---

```js
/* ... */ {
  username: {
    type: DataTypes.TEXT,
    allowNull: false,
    unique: true
  },
} /* ... */
```

만약 이미 존재하는 username을 입력하면 `SequelizeUniqueConstraintError` 가 발생한다.



###### Allowing/disallowing null values

---

기본적으로 `null`은 모델의 모든 칼럼에 허용된 값이다.

하지만 `allowNull : false` 에 의해 이는 허용되지 않을 수 있다.

```js
/* ... */ {
  username: {
    type: DataTypes.TEXT,
    allowNull: false,
    unique: true
  },
} /* ... */
```

위의 상태에서는 `User.create({})` 는 수행되지 않는다.



##### Validators

---

모델 validator은  모델의 각 속성의 format/content/inheritance validation을 구체화 할 수 있도록 한다.

Validaton은 자동적으로 `create` , `update` 그리고 `save` 가 동작될 때 수행된다.

또한 `validate()` 를 통해 수동으로 인스턴스를 검증할 수 있다.



###### Per-attribute validations

---

커스텀한 validator 또는 validator.js에 의해 상속 받은 내장 validator을 사용할 수 있다.



이에 대한 예시는 다음과 같다.

```js
sequelize.define('foo', {
  bar: {
    type: DataTypes.STRING,
    validate: {
      is: /^[a-z]+$/i,          // matches this RegExp
      is: ["^[a-z]+$",'i'],     // same as above, but constructing the RegExp from a string
      not: /^[a-z]+$/i,         // does not match this RegExp
      not: ["^[a-z]+$",'i'],    // same as above, but constructing the RegExp from a string
      isEmail: true,            // checks for email format (foo@bar.com)
      isUrl: true,              // checks for url format (https://foo.com)
      isIP: true,               // checks for IPv4 (129.89.23.1) or IPv6 format
      isIPv4: true,             // checks for IPv4 (129.89.23.1)
      isIPv6: true,             // checks for IPv6 format
      isAlpha: true,            // will only allow letters
      isAlphanumeric: true,     // will only allow alphanumeric characters, so "_abc" will fail
      isNumeric: true,          // will only allow numbers
      isInt: true,              // checks for valid integers
      isFloat: true,            // checks for valid floating point numbers
      isDecimal: true,          // checks for any numbers
      isLowercase: true,        // checks for lowercase
      isUppercase: true,        // checks for uppercase
      notNull: true,            // won't allow null
      isNull: true,             // only allows null
      notEmpty: true,           // don't allow empty strings
      equals: 'specific value', // only allow a specific value
      contains: 'foo',          // force specific substrings
      notIn: [['foo', 'bar']],  // check the value is not one of these
      isIn: [['foo', 'bar']],   // check the value is one of these
      notContains: 'bar',       // don't allow specific substrings
      len: [2,10],              // only allow values with length between 2 and 10
      isUUID: 4,                // only allow uuids
      isDate: true,             // only allow date strings
      isAfter: "2011-11-05",    // only allow date strings after a specific date
      isBefore: "2011-11-05",   // only allow date strings before a specific date
      max: 23,                  // only allow values <= 23
      min: 23,                  // only allow values >= 23
      isCreditCard: true,       // check for valid credit card numbers

      // Examples of custom validators:
      isEven(value) {
        if (parseInt(value) % 2 !== 0) {
          throw new Error('Only even values are allowed!');
        }
      }
      isGreaterThanOtherField(value) {
        if (parseInt(value) <= parseInt(this.otherField)) {
          throw new Error('Bar must be greater than otherField.');
        }
      }
    }
  }
});
```



validator.js에 의해 제공되는 에러 메세지 대신에 커스텀한 에러 메세지를 사용하기 위해서는 평범한 값이나 배열을 객체에 넣어서 제공하면 된다.

```js
isInt: {
  msg: "Must be an integer number of pennies"
}
```

```js
isIn: {
  args: [['en', 'zh']],
  msg: "Must be English or Chinese"
}
```



###### `allowNull` interaction with other validators

---

만약 모델의 특정 필드가 allow null을 허용하지 않고 그 값이 `null` 이면, 모든 validator은 `ValidationError` 을 전달 받는다.

반면 allow null을 허용하고 그 값이 `null` 이면 내장 validator은 넘어가고, 커스텀 validator은 동작한다.



###### Model-wide validations

---

Validation은 또한 필드 validator 이후에 모델을 확인하기 위해서 정의될 수 있다.



다음은 이에대한 예제 코드이다.

```js
class Place extends Model {}
Place.init({
  name: Sequelize.STRING,
  address: Sequelize.STRING,
  latitude: {
    type: DataTypes.INTEGER,
    validate: {
      min: -90,
      max: 90
    }
  },
  longitude: {
    type: DataTypes.INTEGER,
    validate: {
      min: -180,
      max: 180
    }
  },
}, {
  sequelize,
  validate: {
    bothCoordsOrNone() {
      if ((this.latitude === null) !== (this.longitude === null)) {
        throw new Error('Either both latitude and longitude, or neither!');
      }
    }
  }
})
```



#### Raw Queries

---

어떤 경우에는 sequelize에서 제공하는 메서드를 활용하여 쿼리문을 사용하는 것 보다 SQL 쿼리를 사용하는 것이 더 편할 경우가 있다.

이러한 경우에 `seqeuelize.query` 메서드를 활용하면 된다.



기존 함수를 이용할 경우 2가지 요소를 반환 받는다. 

- 결과
- 객체가 포함하는 메타데이터



`sequelize.query` 를 사용할 경우 결과가 메타데이터와 함께 반환된다.

```js
const [results, metadata] = await sequelize.query("UPDATE users SET y = 42 WHERE x = 12");
// Results will be an empty array and metadata will contain the number of affected rows.
```



하지만 sequelize에게 결과의 형식을 어떻게 할지 쿼리 `type` 을 통해 전달한다면 메타데이터에 접근할 필요가 없다.

```js
const { QueryTypes } = require('sequelize');
const users = await sequelize.query("SELECT * FROM `users`", { type: QueryTypes.SELECT });
// We didn't need to destructure the result here - the results were returned directly
```



다른 옵션으로 `model` 이 있다.

`model` 을 옵션으로 설정하면 반환되는 데이터는 그 `model` 의 인스턴스가 된다.

```js
// Callee is the model definition. This allows you to easily map a query to a predefined model
const projects = await sequelize.query('SELECT * FROM projects', {
  model: Projects,
  mapToModel: true // pass true here if you have any mapped fields
});
// Each element of `projects` is now an instance of Project
```



##### "Dotted" attributes and the `nest` option

---

"." 을 포함한 테이블의 속성 이름은 `nest : true` 옵션을 통해 nested 객체 형태로 반환될 수 있다.



우선 `nest : ture`  가 아닌 경우이다.

```js
const { QueryTypes } = require('sequelize');
const records = await sequelize.query('select 1 as `foo.bar.baz`', {
  type: QueryTypes.SELECT
});
console.log(JSON.stringify(records[0], null, 2));
```

```js
{
  "foo.bar.baz": 1
}
```



`nest : true` 인 경우이다.

```js
const { QueryTypes } = require('sequelize');
const records = await sequelize.query('select 1 as `foo.bar.baz`', {
  nest: true,
  type: QueryTypes.SELECT
});
console.log(JSON.stringify(records[0], null, 2));
```

```js
{
  "foo": {
    "bar": {
      "baz": 1
    }
  }
}
```



##### Replacements

---

대체의 경우 2가지 방식으로 수행될 수 있다.

- `:`  : 객체가 전달될 경우, `:key` 는 객체의 keys들로 대체된다.
- `? `  : 배열이 전달될 경우, 배열에서 보이는 순서대로 `?` 가 대체된다.



```js
const { QueryTypes } = require('sequelize');

await sequelize.query(
  'SELECT * FROM projects WHERE status = ?',
  {
    replacements: ['active'],
    type: QueryTypes.SELECT
  }
);

await sequelize.query(
  'SELECT * FROM projects WHERE status = :status',
  {
    replacements: { status: 'active' },
    type: QueryTypes.SELECT
  }
);
```



배열 대체의 경우 자동으로 동작된다.

예를 들어 아래의 코드의 경우 쿼리는 배열의 값과 맞는 상태의 프로젝트를 찾는다.

```js
const { QueryTypes } = require('sequelize');

await sequelize.query(
  'SELECT * FROM projects WHERE status IN(:status)',
  {
    replacements: { status: ['active', 'inactive'] },
    type: QueryTypes.SELECT
  }
);
```



#### Associations

---

Sequelize는 One-To-One, One-To-Many, Many-To-Many와 같은 관계를 지원한다.

이를 위해 Sequelize는 4가지 타입의 연관을 제공한다.

- `HashOne`
- `BelongsTo`
- `HasMany`
- `BelongsToMany`



##### Defining the Sequelize associations

---

```js
const A = sequelize.define('A', /* ... */);
const B = sequelize.define('B', /* ... */);

A.hasOne(B); // A HasOne B
A.belongsTo(B); // A BelongsTo B
A.hasMany(B); // A HasMany B
A.belongsToMany(B, { through: 'C' }); // A BelongsToMany B through the junction table C
```

위의 것들은 모두 2번째 인자로 옵션을 받는다.

```js
A.hasOne(B, { /* options */ });
A.belongsTo(B, { /* options */ });
A.hasMany(B, { /* options */ });
A.belongsToMany(B, { through: 'C', /* options */ });
```



관계가 정의될 때 순서는 의미가 있다.

위의 경우를 예를들면, `A` 는 source 모델이고 `B` 는 target 모델이다.



`A.hasOne(B)` 관계는 `A` 와 `B` 사이가 One-To-One 관계라는 뜻이고 `FK` 는 target 모델인 `B` 에 정의된다.

`A.belongsTo(B)` 관계는  `A` 와 `B` 사이가 One-To-One 관계라는 뜻이고 `FK` 는 source 모델인 `A` 에 정의된다.

`A.hasMany(B)` 관계는 `A` 와 `B` 사이가 One-To-Many 관계라는 뜻이고 `FK` 는 target 모델인 `B` 에 정의된다.

이 3가지 메서드의 경우 Sequelize가 자동적으로 `FK` 를 적절한 모델에 추가한다.



`A.belongsToMany(B, { through: 'C'})` 관계는 Many-To-Many 관계라는 뜻이고 `aId` 그리고 `bId` 와 같은 `Fk` 를 가지고 있는 새로운 테이블 `C` 를 사용한다.

Sequelize가 자동적으로 `c`  모델을 만들고 적절한 `FK` 를 정의한다.



##### Creating the standard relationships

---

sequelize의 관계는 쌍으로 정의된다.

- One-To-One 관계를 만들기 위해서는 `hasOne` 그리고 `belongsTo` 관계가 함께 쓰인다.
- One-To-Many 관계를 만들기 위해서는 `hasMany` 그리고 `belongsTo` 관계가 함께 쓰인다.
- Many-To-Many 관계를 만들기 위해서는 2개의 `belongsToMany` 가 쓰인다.



##### One-To-One relationships

---



###### Options

---

`onDelete` and `onUpdate`

```js
Foo.hasOne(Bar, {
  onDelete: 'RESTRICT',
  onUpdate: 'RESTRICT'
});
Bar.belongsTo(Foo);
```

옵션의 값으로 가능한 선택지는 다음과 같다.

`RESTRICT`, `CASCADE`, `NO ACTION`, `SET DEFAULT` 그리고  `SET NULL`

One-To-One 관계는 `ON DELETE` 은  `SET NULL`  그리고  `ON UPDATE` 는 `CASCADE` 가 기본 값이다.



###### Customizing the foreign key

---

```js
// Option 1
Foo.hasOne(Bar, {
  foreignKey: 'myFooId'
});
Bar.belongsTo(Foo);

// Option 2
Foo.hasOne(Bar, {
  foreignKey: {
    name: 'myFooId'
  }
});
Bar.belongsTo(Foo);

// Option 3
Foo.hasOne(Bar);
Bar.belongsTo(Foo, {
  foreignKey: 'myFooId'
});

// Option 4
Foo.hasOne(Bar);
Bar.belongsTo(Foo, {
  foreignKey: {
    name: 'myFooId'
  }
});
```

위의 코드와 같이 `FK` 를 커스텀 할 수 있다.



```js
const { DataTypes } = require("Sequelize");

Foo.hasOne(Bar, {
  foreignKey: {
    // name: 'myFooId'
    type: DataTypes.UUID
  }
});
Bar.belongsTo(Foo);
```

위의 코드 처럼 이름이 아니라 고유 식별자를 설정할 수도 있다.



##### One-To-Many relationships

---



###### Options

---

옵션의 경우 위의 One-To-One 관계와 동일하다.



##### Many-To-Many relationships

---

Many-To-Many 관계의 경우 다른 관계들과 다르게 단순히 `FK` 만 추가하는 것으로 관계를 설명할 수 없다.

그렇기데 Junction Model을 사용한다.

이는 2개의 `FK` 를 가지고 관계를 추적하는 여분의 모델이다.

Juntion 테이블은 join 테이블 그리고 through 테이블이라 불리기도 한다.



다음은 이에 대한 예제 코드이다.

```js
const Movie = sequelize.define('Movie', { name: DataTypes.STRING });
const Actor = sequelize.define('Actor', { name: DataTypes.STRING });
Movie.belongsToMany(Actor, { through: 'ActorMovies' });
Actor.belongsToMany(Movie, { through: 'ActorMovies' });
```



조금 더 구체적으로 설정해보면 다음과 같다.

```js
const Movie = sequelize.define('Movie', { name: DataTypes.STRING });
const Actor = sequelize.define('Actor', { name: DataTypes.STRING });
const ActorMovies = sequelize.define('ActorMovies', {
  MovieId: {
    type: DataTypes.INTEGER,
    references: {
      model: Movie, // 'Movies' would also work
      key: 'id'
    }
  },
  ActorId: {
    type: DataTypes.INTEGER,
    references: {
      model: Actor, // 'Actors' would also work
      key: 'id'
    }
  }
});
Movie.belongsToMany(Actor, { through: ActorMovies });
Actor.belongsToMany(Movie, { through: ActorMovies });
```



###### Options

---

One-To-One 그리고 One-To-Many 와 달리 Many-To-Many 관계의  `ON UPDATE` 그리고 `ON DELETE` 의 기본 값은 `CASCADE` 이다.



##### Basics of queries involving associations

---

가장 관계와 관련이 있는 쿼리는 `read` 이다.



이를 알아보기 위해 아래와 같은 코드를 준비했다.

```js
// This is the setup of our models for the examples below
const Ship = sequelize.define('ship', {
  name: DataTypes.TEXT,
  crewCapacity: DataTypes.INTEGER,
  amountOfSails: DataTypes.INTEGER
}, { timestamps: false });
const Captain = sequelize.define('captain', {
  name: DataTypes.TEXT,
  skillLevel: {
    type: DataTypes.INTEGER,
    validate: { min: 1, max: 10 }
  }
}, { timestamps: false });
Captain.hasOne(Ship);
Ship.belongsTo(Captain);
```

`FK` 는 `allow null` 상태(default)이다. 

이는 Ship이 Captain 없이 존재할 수 있다는 뜻이다.



##### Fetching associations - Eager Loading vs Lazy Loading

---

Eager Loading 그리고 Lazy Loading의 개념을 이해하는 것은 Sequelize에서 동작하는 fetching 관계에 대해 이해하는데 기본적인 것이다.

Lazy Loading은 우리가 정말로 원할 때만 관계있는 데이터를 fetching 하는 기술이다.

Eager Loading은 처음부터 한번에 모든 것을 fetching 하는 기술이다.



다음은 Lazy Loading의 예이다.

```js
const awesomeCaptain = await Captain.findOne({
  where: {
    name: "Jack Sparrow"
  }
});
// Do stuff with the fetched captain
console.log('Name:', awesomeCaptain.name);
console.log('Skill Level:', awesomeCaptain.skillLevel);
// Now we want information about his ship!
const hisShip = await awesomeCaptain.getShip();
// Do stuff with the ship
console.log('Ship Name:', hisShip.name);
console.log('Amount of Sails:', hisShip.amountOfSails);
```



> `getShip()` 은 Sequelize가 자동적으로 `Captain` 인스턴스에 추가한 인스턴스 메서드이다.



다음은 Eager Loading의 예이다.

```js
const awesomeCaptain = await Captain.findOne({
  where: {
    name: "Jack Sparrow"
  },
  include: Ship
});
// Now the ship comes with it
console.log('Name:', awesomeCaptain.name);
console.log('Skill Level:', awesomeCaptain.skillLevel);
console.log('Ship Name:', awesomeCaptain.ship.name);
console.log('Amount of Sails:', awesomeCaptain.ship.amountOfSails);
```

Eager Loading의 경우 `include` 옵션을 사용해서 Sequelize에서 수행되었다.

오직 하나의 쿼리가 DB에서 수행된 것을 볼 수 있다.



##### Creating, updating and deleting

---

위에서 볼 수 있듯 데이터를 fetching할 기본적인 쿼리는 관계를 포함한다.

creating, updating 그리고 deleting에서도 동일하다.



```js
// Example: creating an associated model using the standard methods
Bar.create({
  name: 'My Bar',
  fooId: 5
});
// This creates a Bar belonging to the Foo of ID 5 (since fooId is
// a regular column, after all). Nothing very clever going on here.
```

이는 일반적인 모델 쿼리를 사용한 예제이다.



##### Association Aliases & Custom Foreign Keys

---

위의 모든 예제에서 Sequelize가 자동적으로 `FK` 의 이름을 정의했다.

하지만 이 `FK` 의 이름을 쉽게 커스텀할 수 있다.

`FK` 를 커스텀할 수 있는 3가지 방법은 다음과 같다.

- `FK` 의 이름을 직접적으로 제공하는 방법
- 별명을 정의하는 바법
- 둘다 하는 방법



###### Recap: the default setup

---

`Ship.belongsTo(Captain)` 과 같은 간단한 사용의 경우, sequelize가 `FK` 의 이름을 자동적으로 만들어 준다.



```js
Ship.belongsTo(Captain); // This creates the `captainId` foreign key in Ship.

// Eager Loading is done by passing the model to `include`:
console.log((await Ship.findAll({ include: Captain })).toJSON());
// Or by providing the associated model name:
console.log((await Ship.findAll({ include: 'captain' })).toJSON());

// Also, instances obtain a `getCaptain()` method for Lazy Loading:
const ship = Ship.findOne();
console.log((await ship.getCaptain()).toJSON());
```



###### Providing the foreign key name directly

---

관계를 정의하는 옵션에서 `FK` 의 이름을 직접적으로 제공할 수도 있다.



```js
Ship.belongsTo(Captain, { foreignKey: 'bossId' }); // This creates the `bossId` foreign key in Ship.

// Eager Loading is done by passing the model to `include`:
console.log((await Ship.findAll({ include: Captain })).toJSON());
// Or by providing the associated model name:
console.log((await Ship.findAll({ include: 'Captain' })).toJSON());

// Also, instances obtain a `getCaptain()` method for Lazy Loading:
const ship = Ship.findOne();
console.log((await ship.getCaptain()).toJSON());
```



###### Defining an Alias

---

`FK` 를 커스텀하는 것보다 별명을 정의하는 것은 더 강력하다.



```js
Ship.belongsTo(Captain, { as: 'leader' }); // This creates the `leaderId` foreign key in Ship.

// Eager Loading no longer works by passing the model to `include`:
console.log((await Ship.findAll({ include: Captain })).toJSON()); // Throws an error
// Instead, you have to pass the alias:
console.log((await Ship.findAll({ include: 'leader' })).toJSON());
// Or you can pass an object specifying the model and alias:
console.log((await Ship.findAll({
  include: {
    model: Captain,
    as: 'leader'
  }
})).toJSON());

// Also, instances obtain a `getLeader()` method for Lazy Loading:
const ship = Ship.findOne();
console.log((await ship.getLeader()).toJSON());
```

별명은 2개의 다른 관계를 같은 모델 사이에서 정의할 때 유용하다.



###### Doing both things

---

우리는 위의 둘다를 활용할 수도 있다.

```js
Ship.belongsTo(Captain, { as: 'leader', foreignKey: 'bossId' }); // This creates the `bossId` foreign key in Ship.

// Since an alias was defined, eager Loading doesn't work by simply passing the model to `include`:
console.log((await Ship.findAll({ include: Captain })).toJSON()); // Throws an error
// Instead, you have to pass the alias:
console.log((await Ship.findAll({ include: 'leader' })).toJSON());
// Or you can pass an object specifying the model and alias:
console.log((await Ship.findAll({
  include: {
    model: Captain,
    as: 'leader'
  }
})).toJSON());

// Also, instances obtain a `getLeader()` method for Lazy Loading:
const ship = Ship.findOne();
console.log((await ship.getLeader()).toJSON());
```



##### Special methods/mixins added to instances

---

2개의 모델사이에 관계가 정의되면, 그 모델들의 인스턴스는 그들의 상대와 상호작용할 수 있는 특별한 메서드를 얻는다.



예를 들어 `Foo` 그리고 `Bar` 이라는 모델을 가지고 있다.



###### `Foo.hasOne(Bar)`

- `fooInstance.getBar()`
- `fooInstance.setBar()`
- `fooInstance.createBar()`

```js
const foo = await Foo.create({ name: 'the-foo' });
const bar1 = await Bar.create({ name: 'some-bar' });
const bar2 = await Bar.create({ name: 'another-bar' });
console.log(await foo.getBar()); // null
await foo.setBar(bar1);
console.log((await foo.getBar()).name); // 'some-bar'
await foo.createBar({ name: 'yet-another-bar' });
const newlyAssociatedBar = await foo.getBar();
console.log(newlyAssociatedBar.name); // 'yet-another-bar'
await foo.setBar(null); // Un-associate
console.log(await foo.getBar()); // null
```



###### `Foo.belongsTo(Bar)`

 `Foo.hasOne(Bar)` 의 형식과 같다.

- `fooInstance.getBar()`
- `fooInstance.setBar()`
- `fooInstance.createBar()`



###### `Foo.hasMany(Bar)`

- `fooInstance.getBars()`
- `fooInstance.countBars()`
- `fooInstance.hasBar()`
- `fooInstance.hasBars()`
- `fooInstance.setBars()`
- `fooInstance.addBar()`
- `fooInstance.addBars()`
- `fooInstance.removeBar()`
- `fooInstance.removeBars()`
- `fooInstance.createBar()`

```js
const foo = await Foo.create({ name: 'the-foo' });
const bar1 = await Bar.create({ name: 'some-bar' });
const bar2 = await Bar.create({ name: 'another-bar' });
console.log(await foo.getBars()); // []
console.log(await foo.countBars()); // 0
console.log(await foo.hasBar(bar1)); // false
await foo.addBars([bar1, bar2]);
console.log(await foo.countBars()); // 2
await foo.addBar(bar1);
console.log(await foo.countBars()); // 2
console.log(await foo.hasBar(bar1)); // true
await foo.removeBar(bar2);
console.log(await foo.countBars()); // 1
await foo.createBar({ name: 'yet-another-bar' });
console.log(await foo.countBars()); // 2
await foo.setBars([]); // Un-associate all previously associated bars
console.log(await foo.countBars()); // 0
```



###### `Foo.belongsToMany(Bar, { through: Baz })`

`Foo.hasMany(Bar)` 와 같은 형식이다.

- `fooInstance.getBars()`
- `fooInstance.countBars()`
- `fooInstance.hasBar()`
- `fooInstance.hasBars()`
- `fooInstance.setBars()`
- `fooInstance.addBar()`
- `fooInstance.addBars()`
- `fooInstance.removeBar()`
- `fooInstance.removeBars()`
- `fooInstance.createBar()`



> - 변수 = sourceModel.find 의 경우 변수.getTargetModel 류를 사용할 수 있지만 변수.createTargetModel 류를 사용할 수 없다.
>   대신 데이터변수 = 모델.create()를 통해 데이터변수를 만들고 변수.addTargetModel을 사용하면 연관 관계를 가지게 하면서 데이터를 만들 수 있다.
>
>   다음은 이에 관한 예시코드 이다.
>
>   ```js
>   data = await Data.create({ Etitle : input.Etitle ,Esubtitle : input.Esubtitle ,Ecomment : input.Ecomment});
>   userData.addData(data);
>   ```
>
>   
>
> - 변수 = sourceModel.create 의 경우 변수.createTargetModel 을 사용할 수 있고 변수.getTargetModel 류도 사용할 수 있다.



**연관관계인 모델 사이에서 데이터를 생성, 수정, 삭제할 때는 위의 메서드를 사용하는 것이 더 편리하다.**



##### Why associations are defined in pairs?

---

Sequelize가 2개의 모델 사이에 관계를 정의할 때, 오직 source 모델만 그것을 안다.

예를 들어 `Foo.hansOne(Bar)` 를 사용할 때, `Foo` 만 관계의 존재에 대해 알고 있다.

이것이 `Foo` 인스턴스만  `getBar()`, `setBar()` 그리고 `createBar()` 와 같은 메서드를 갖고 `Bar` 인스턴스는 가지지 않는 이유이다.

이와 유사하게 우리는  `Foo.findOne({ include: Bar })` 는 수행할 수 있지만 `Bar.findOne({ include: Foo })` 는 수행할 수 없다.



그렇기에 Sequelize를 강력하게 사용하려면, 우리는 관계를 쌍으로 설정할 필요가 있다.

그래서 모든 모델이 관계에 대해 알필요가 있다.



만약 관계를 쌍으로 설정하지 않는다면 다음과 같은 결과를 확인할 수 있고

```js
// This works...
await Foo.findOne({ include: Bar });

// But this throws an error:
await Bar.findOne({ include: Foo });
// SequelizeEagerLoadingError: foo is not associated to bar!
```



 관계를 쌍으로 설정한다면 다음과 같은 결과를 확인할 수 있다.

```js
// This works!
await Foo.findOne({ include: Bar });

// This also works!
await Bar.findOne({ include: Foo });
```



##### Multiple associations involving the same models

----

Sequelize에서 같은 모델들간에 다양한 관계를 정의하는 것은 가능하다.



다음은 이에대한 예제이다.

```js
Team.hasOne(Game, { as: 'HomeTeam', foreignKey: 'homeTeamId' });
Team.hasOne(Game, { as: 'AwayTeam', foreignKey: 'awayTeamId' });
Game.belongsTo(Team);
```



##### Creating associations referencing a field which is not the primary key

---

Sequelize는 관계를 `PK` 가 아닌 다른 필드를 사용해서 설정할 수 있다.

하지만 그 필드는 unique 제약 조건을 가져야 한다.



###### For `belongsTo` relationships

---



```js
const Ship = sequelize.define('ship', { name: DataTypes.TEXT }, { timestamps: false });
const Captain = sequelize.define('captain', {
  name: { type: DataTypes.TEXT, unique: true }
}, { timestamps: false });
```

```js
Ship.belongsTo(Captain, { targetKey: 'name', foreignKey: 'captainName' });
// This creates a foreign key called `captainName` in the source model (Ship)
// which references the `name` field from the target model (Captain).
```

target 모델에서 `id` 를 참조하는 대신에 `name` 칼럼을 참조했다.

그렇기에 이를 명시하기 위해, `targetKey` 를 정의한다.



###### For `hasOne` and `hasMany` relationships

---



```js
const Foo = sequelize.define('foo', {
  name: { type: DataTypes.TEXT, unique: true }
}, { timestamps: false });
const Bar = sequelize.define('bar', {
  title: { type: DataTypes.TEXT, unique: true }
}, { timestamps: false });
const Baz = sequelize.define('baz', { summary: DataTypes.TEXT }, { timestamps: false });
Foo.hasOne(Bar, { sourceKey: 'name', foreignKey: 'fooName' });
Bar.hasMany(Baz, { sourceKey: 'title', foreignKey: 'barTitle' });
// [...]
await Bar.setFoo("Foo's Name Here");
await Baz.addBar("Bar's Title Here");
```

source 모델에서 `id` 를 참조하는 대신에 `name` , `title` 칼럼을 참조했다.

그렇기에 이를 명시하기 위해, `sourceKey` 를 정의한다.



###### For `belongsToMany` relationships

----



```js
const Foo = sequelize.define('foo', {
  name: { type: DataTypes.TEXT, unique: true }
}, { timestamps: false });
const Bar = sequelize.define('bar', {
  title: { type: DataTypes.TEXT, unique: true }
}, { timestamps: false });
```

```js
Foo.belongsToMany(Bar, { through: 'foo_bar' });
// This creates a junction table `foo_bar` with fields `fooId` and `barId`
```

```js
Foo.belongsToMany(Bar, { through: 'foo_bar', targetKey: 'title' });
// This creates a junction table `foo_bar` with fields `fooId` and `barTitle`
```

```js
Foo.belongsToMany(Bar, { through: 'foo_bar', sourceKey: 'name' });
// This creates a junction table `foo_bar` with fields `fooName` and `barId`
```

```js
Foo.belongsToMany(Bar, { through: 'foo_bar', sourceKey: 'name', targetKey: 'title' });
// This creates a junction table `foo_bar` with fields `fooName` and `barTitle`
```

이는 위의 sourcekey와 targetkey를 모두 활용하여 through 테이블을 만드는 것을 볼 수 있다.



### Migrations

---

소스 코드를 관리하기 위해서 Git이 있는 것처럼 DB의 변화를 관리하기 위해서는 migrations을 사용할 수 있다.

migrations를 사용하면 존재하는 DB를 다른 상태로 전환시킬 수 있다.

이는 어떻게 새로운 상태를 얻을 것인지 그리고 어떻게 이전 상태로 되돌릴 것인지 적혀있는 migration 파일에 의해 이루어진다.



Sequelize에서 Migration은 `up` 그리고 `down`  함수를 가지고 있는 javascript 파일이다.

이것은 어떻게 migration을 수행하고 되돌릴지 지정한다.

함수의 경우 우리가 직접 작성해야 하지만 작동의 경우 CLI가 자동으로 수행해 준다.

`sequelize.query` 그리고 다른 메서드의 도움을 받아 우리가 원하는 동작을 수행할 수 있다.



#### Installing the CLI

---

```
npm install --save-dev sequelize-cli
```



#### Project bootstrapping

---

```
npx sequelize-cli init
```

이를 입력하면 다음과 같은 폴더가 생성된다.



- `config`
  CLI가 어떻게 DB와 연결할지 알려주는 설정 파일을 포함하고 있다. 
- `models`
  프로젝트의 모든 모델을 포함하고 있다.
- `migrations`
  모든 migration 파일을 포함하고 있다.
- `seeders`
  모든 seed 파일을 포함하고 있다.



#### Configuration

---

```json
{
  "development": {
    "username": "root",
    "password": null,
    "database": "database_development",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```

위와 같은 파일을 통해 DB와 연결할 수 있다.



#### Creating the first Model (and Migration)

---

`model:generate` 와 같은 명령을 통해 모델을 만들 수 있다.



```
npx sequelize-cli model:generate --name User --attributes firstName:string,lastName:string,email:string
```

위와 같은 명령을 통해 모델을 만든다면 `models`  폴더에 `user` 파일이 생성될 것이다.

그리고 `migrations` 폴더에 `XXXXXXXXXXXXXX-create-user.js` 와 같은 이름의 migration 파일이 생성될 것이다.



#### Running Migrations

---

```
npx sequelize-cli db:migrate
```



#### Undoing Mirgartions

---

```
npx sequelize-cli db:migrate:undo
```



#### Creating the first Seed

---

다음과 같은 명령을 통해  `User` 테이블의 seed를 만들 수 있다.

```
npx sequelize-cli seed:generate --name demo-user
```



이 명령ㅣ `seeders` 폴더에 seed 파일을 만들 것이다.

`XXXXXXXXXXXXXX-demo-user.js` 와 같은 이름의 파일을 볼 수 있을 것이다.

이는 migration 파일과 같은 up/down  함수를 가지고 있다.



우리는 이 파일을 사용해서다음과 같이  `User` 테이블에서 사용할 seed를 만들 수 있다.

```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Users', [{
      firstName: 'John',
      lastName: 'Doe',
      email: 'example@example.com',
      createdAt: new Date(),
      updatedAt: new Date()
    }]);
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Users', null, {});
  }
};
```



#### Running Seeds 

----

```
npx sequelize-cli db:seed:all
```



#### Undoing Seeds

---

```
npx sequelize-cli db:seed:undo
```



#### Migration Skeleton

---

```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    // logic for transforming into the new state
  },
  down: (queryInterface, Sequelize) => {
    // logic for reverting the changes
  }
}
```

migration 파일의 구조는 위와 같다.



우리는 `migration:generate` 를 사용해서 이 파일을 만들 수 있다.

이는 `xxx-migration-skeleton.js` 와 같은 파일을 migration 폴더에 만들어 줄 것이다.



`queryInterface` 객체가 DB를 조작하게 해줄 것이다.

`up` 혹은 `down` 함수는 반드시  `Promise` 를 반환한다. 



```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Person', {
      name: Sequelize.DataTypes.STRING,
      isBetaMember: {
        type: Sequelize.DataTypes.BOOLEAN,
        defaultValue: false,
        allowNull: false
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Person');
  }
};
```

위는 이를 구현해본 예제 코드이다.



```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.sequelize.transaction(t => {
      return Promise.all([
        queryInterface.addColumn('Person', 'petName', {
          type: Sequelize.DataTypes.STRING
        }, { transaction: t }),
        queryInterface.addColumn('Person', 'favoriteColor', {
          type: Sequelize.DataTypes.STRING,
        }, { transaction: t })
      ]);
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.sequelize.transaction(t => {
      return Promise.all([
        queryInterface.removeColumn('Person', 'petName', { transaction: t }),
        queryInterface.removeColumn('Person', 'favoriteColor', { transaction: t })
      ]);
    });
  }
};
```

 위의 예제는 성공적으로 지시가 수행되거나 실패할 경우 롤백할 수 있는 자동 관리 transaction을 사용한 예제이다.



```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Person', {
      name: Sequelize.DataTypes.STRING,
      isBetaMember: {
        type: Sequelize.DataTypes.BOOLEAN,
        defaultValue: false,
        allowNull: false
      },
      userId: {
        type: Sequelize.DataTypes.INTEGER,
        references: {
          model: {
            tableName: 'users',
            schema: 'schema'
          },
          key: 'id'
        },
        allowNull: false
      },
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Person');
  }
}
```

위의 예제는 FK를 가지고 있는 migration 예제이다.

FK를 구체화하기 위해서 위와 같이 reference를 사용할 수 있다.



```js
module.exports = {
  async up(queryInterface, Sequelize) {
    const transaction = await queryInterface.sequelize.transaction();
    try {
      await queryInterface.addColumn(
        'Person',
        'petName',
        {
          type: Sequelize.DataTypes.STRING,
        },
        { transaction }
      );
      await queryInterface.addIndex(
        'Person',
        'petName',
        {
          fields: 'petName',
          unique: true,
          transaction,
        }
      );
      await transaction.commit();
    } catch (err) {
      await transaction.rollback();
      throw err;
    }
  },
  async down(queryInterface, Sequelize) {
    const transaction = await queryInterface.sequelize.transaction();
    try {
      await queryInterface.removeColumn('Person', 'petName', { transaction });
      await transaction.commit();
    } catch (err) {
      await transaction.rollback();
      throw err;
    }
  }
};
```

위의 예제는 async/await를 사용하는 migration이다.

위와 같이 수동으로 tranction을 관리할 수도 있다.



#### Dynamic configuration

---

설정 파일은 JSON 파일인  `config.json` 이 기본이다.

하지만 동적 설정이 필요한 경우가 있다.



다행이도 Sequelize CLI는 `.json` 그리고 `.js`  모두 읽을 수 있다.




<details>
<summary>참고</summary>
<a href=https://sequelize.org/docs/v6/getting-started/>https://sequelize.org/docs/v6/getting-started/</a></br>
<a href=https://jaehoney.tistory.com/141?category=869692>https://jaehoney.tistory.com/141?category=869692</a>
</details>
