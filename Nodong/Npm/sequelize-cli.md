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



> 만약 DB가 아직 존재하지 않는다면 `db:create` 를 사용해서 만들 수 있다.



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

위의 명령을 통해 우리는 DB에 테이블을 만들 수 있다.

위의 명령은 다음과 같은 과정을 수행한다.

1. `SequelizeMeta` 라는 테이블을 DB에 생성한다. 이 테이블은 어떤 migration이 현제 DB에 수행되었는지 기록한다.
2. 아직 수행되지 않은 migration 파일을 찾는다. 이는 `SequelizeMeta`  테이블에 의해서 가능해진다. 
3. migration 파일을 기반으로 테이블의 칼럼을 생성한다.



#### Undoing Mirgartions

---

```
npx sequelize-cli db:migrate:undo
```

위의 명령은 가장 최근의 migration 상태로 되돌려주는 명령이다.



`db:migrate:undo:all` 을 통해서 모든 migration을 되돌려 처음 상태로 돌아갈 수도 있고, `--to` 옵션을 사용해서 특정 migration 만 되돌릴 수도 있다.



#### Creating the first Seed

---

다음과 같은 명령을 통해  `User` 테이블의 seed를 만들 수 있다.

```
npx sequelize-cli seed:generate --name demo-user
```



이 명령니 `seeders` 폴더에 seed 파일을 만들 것이다.

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

