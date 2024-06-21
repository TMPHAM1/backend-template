# NestJS/TypeORM/GraphQL/PostgresQL

NestJS boilerplate with TypeORM, GraphQL and PostgreSQL

## Open for Contribution

Totally open for any Pull Request, please feel free to contribute in any ways.
There can be errors related with type or something. It would be very helpful to me for you to fix these errors.

## [NestJS](https://docs.nestjs.com/)

Base NestJS, We like it

## [TypeORM](https://typeorm.io/)

We use [Nestjs/TypeORM](https://docs.nestjs.com/techniques/database)
In this template, We've been trying not to use `Pure SQL` to make the most of TypeORM.

## [PostgresQL Database](https://www.postgresql.org/)

We use postgresQL for backend database, The default database taht will be used is named 'postgres'
You have to have postgresql Database server before getting started.
You can use [Docker postgresQL](https://hub.docker.com/_/postgres) to have server easily

## [GraphQL](https://graphql.org/)

##### packages: graphql, apollo-server-express and @nestjs/graphql, [graphqlUpload](https://www.npmjs.com/package/graphql-upload) ...

We use GraphQL in a Code First approach (our code will create the GraphQL Schemas).

We don't use [swagger](https://docs.nestjs.com/openapi/introduction) now, but you can use this if you want to.
You can see [playground](http://localhost:8000/graphql)

We use apollographql as playground. but if you want to use default playground, you can do like below.

```js
// setting.service.ts

GraphQLModule.forRootAsync <
  ApolloDriverConfig >
  {
    ...
    useFactory: (configService: ConfigService) => ({
      ...
      playground: true,
      ...
    }),
    ...
  };
```

### Protected queries/mutation by user role with guard

Some of the GraphQL queries are protected by a NestJS Guard (`GraphqlPassportAuthGuard`) and requires you to be authenticated (and some also requires to have the Admin role).
You can solve them with Sending JWT token in `Http Header` with the `Authorization`.

```json
# Http Header
{
  "Authorization": "Bearer TOKEN"
}
```

#### Example of some protected GraphQL

- getMe (must be authenticated)
- All methods generated by the generator (must be authenticated and must be admin)

## License

MIT

## Custom CRUD

To make most of GraphQL's advantage, We created its own api, such as GetMany or GetOne.
We tried to make it as comfortable as possible, but if you find any mistakes or improvements, please point them out or promote them.

You can see detail in folder [/src/common/graphql](/src/common/graphql) files

```js
// query
query($input:GetManyInput) {
  getManyPlaces(input:$input){
    data{
      id
      logitude
      count
    }
  }
}
```

```js
// variables
{
  input: {
    pagination: {
      size: 10,
      page: 0, // Started from 0
    },
    order: { id: 'DESC' },
    dataType: 'data', //all or count or data - default: all
    where: {
      id: 3,
    },
  },
};
```

You can see detail [here](./process-where.md).

## Code generator

There is [CRUD Generator in NestJS](https://docs.nestjs.com/recipes/crud-generator).
In this repository, It has its own generator with [plopjs](https://plopjs.com/documentation/).
You can use like below.

```bash
$ yarn g
```

## Getting Started

### Installation

Before you start, make sure you have a recent version of [NodeJS](http://nodejs.org/) environment _>=14.0_ with NPM 6 or Yarn.

The first thing you will need is to install NestJS CLI.

```bash
$ yarn -g @nestjs/cli
```

And do install the dependencies

```bash
$ yarn install # or npm install
```

### Run

for development

```bash
$ yarn dev # or npm run dev
```

for production

```bash
$ yarn build # or npm run build
$ yarn start # or npm run start
```

or run with docker following below

## Docker

### Docker-compose installation

Download docker from [Official website](https://docs.docker.com/compose/install)

### Before getting started

Before running Docker, you need to create an env file named `.production.env`.
The content should be modified based on `.example.env`.
The crucial point is that DB_HOST must be set to 'postgres'.

### Run

Open terminal and navigate to project directory and run the following command.

```bash
# Only for prduction
$ docker-compose --env-file ./.production.env up
```

### Note

If you want to use docker, you have to set DB_HOST in .production.env to be `postgres`.
The default set is `postgres`

### Only database

You can just create postgresql by below code, sync with .development.env

```bash
$ docker run -p 5432:5432 --name postgres -e POSTGRES_PASSWORD=1q2w3e4r -d postgres
```

## TDD

### Introduction

[`@nestjs/testing`](https://docs.nestjs.com/fundamentals/testing) = `supertest` + `jest`

### Before getting started

Before starting the test, you need to set at least jwt-related environment variables in an env file named `.test.env`.

### Unit Test (Use mock)

Unit test(with jest mock) for services & resolvers (\*.service.spec.ts & \*.resolver.spec.ts)

#### Run

```bash
$ yarn test
```

### Integration Test (Use in-memory DB)

Integration test(with [pg-mem](https://github.com/oguimbal/pg-mem)) for modules (\*.module.spec.ts)

#### Run

```bash
$ yarn test
```

### End To End Test (Use docker)

E2E Test(with docker container)

#### Run

```bash
$ yarn test:e2e:docker
```

## CI

### Github actions

To ensure github actions execution, please set the 'ENV' variable within your github actions secrets as your .test.env configuration.

**Note:** Github Actions does not recognize newline characters. Therefore, you must remove any newline characters from each environment variable value in your `.env` file, ensuring that the entire content is on a single line when setting the Secret. If you need to use an environment variable value that includes newline characters, encode the value using Base64 and store it in the Github Secret, then decode it within the workflow.

ex)

```bash
JWT_PRIVATE_KEY= -----BEGIN RSA PRIVATE KEY-----...MIIEogIBAAKCAQBZ...-----END RSA PRIVATE KEY-----
```

### [Husky v9](https://github.com/typicode/husky)

#### Before getting started

```bash
$ yarn prepare
```

#### Pre commit

[You can check detail here](./.husky/pre-commit)

Before commit, The pre-commit hooks is executed.

Lint checks have been automated to run before a commit is made.

If you want to add test before commit actions, you can add follow line in [pre-commit](./.husky/pre-commit) file.

```bash
...
yarn test
...
```

#### Pre push

[You can check detail here](./.husky/pre-push)

The pre-push hooks is executed before the push action.

The default rule set in the pre-push hook is to prevent direct pushes to the main branch.

If you want to enable this action, you should uncomment the lines in the pre push file.

### [SWC Compiler](https://docs.nestjs.com/recipes/swc)

[SWC](https://swc.rs/) (Speedy Web Compiler) is an extensible Rust-based platform that can be used for both compilation and bundling. Using SWC with Nest CLI is a great and simple way to significantly speed up your development process.

#### SWC + Jest error resolution

After applying `SWC`, the following error was displayed in jest using an in-memory database (`pg-mem`):

```bash
    QueryFailedError: ERROR: function obj_description(regclass,text) does not exist
    HINT: 🔨 Please note that pg-mem implements very few native functions.

                👉 You can specify the functions you would like to use via "db.public.registerFunction(...)"

    🐜 This seems to be an execution error, which means that your request syntax seems okay,
        but the resulting statement cannot be executed → Probably not a pg-mem error.

    *️⃣ Failed SQL statement: SELECT "table_schema", "table_name", obj_description(('"' || "table_schema" || '"."' || "table_name" || '"')::regclass, 'pg_class') AS table_comment FROM "information_schema"."tables" WHERE ("table_schema" = 'public' AND "table_name" = 'user');

    👉 You can file an issue at https://github.com/oguimbal/pg-mem along with a way to reproduce this error (if you can), and  the stacktrace:
```

`pg-mem` is a library designed to emulate `PostgreSQL`, however, it does not support all features, which is why the above error occurred.

This error can be resolved by implementing or overriding existing functions. Below is the function implementation for the resolution.
Related issues can be checked [here](https://github.com/oguimbal/pg-mem/issues/380).

```ts
db.public.registerFunction({
  name: 'obj_description',
  args: [DataType.text, DataType.text],
  returns: DataType.text,
  implementation: () => 'test',
});
```

## Todo

- [x] TDD

  - [x] Unit Test (Use mock)
  - [x] Integration Test (Use in-memory DB)
  - [x] End To End Test (Use docker)

- [ ] Add Many OAUths (Both of front and back end)

  - [ ] Kakao
  - [ ] Google
  - [ ] Apple
  - [ ] Naver

- [x] CI

  - [x] Github actions
  - [x] husky

- [x] GraphQL Upload
- [x] Healthcheck
- [x] Divide usefactory
- [x] SWC Compiler
- [ ] Refresh Token
- [ ] Redis
- [ ] ElasticSearch
- [ ] Caching
- [ ] Graphql Subscription
- [ ] Remove lodash
- [ ] [CASL](https://docs.nestjs.com/security/authorization#integrating-casl)
