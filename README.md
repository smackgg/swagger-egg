# swagger-egg

[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][codecov-image]][codecov-url]
[![David deps][david-image]][david-url]
[![Known Vulnerabilities][snyk-image]][snyk-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/swagger-egg.svg?style=flat-square
[npm-url]: https://npmjs.org/package/swagger-egg
[travis-image]: https://img.shields.io/travis/jsonma/swagger-egg.svg?style=flat-square
[travis-url]: https://travis-ci.org/jsonma/swagger-egg
[codecov-image]: https://img.shields.io/codecov/c/github/jsonma/swagger-egg.svg?style=flat-square
[codecov-url]: https://codecov.io/github/jsonma/swagger-egg?branch=master
[david-image]: https://img.shields.io/david/jsonma/swagger-egg.svg?style=flat-square
[david-url]: https://david-dm.org/jsonma/swagger-egg
[snyk-image]: https://snyk.io/test/npm/swagger-egg/badge.svg?style=flat-square
[snyk-url]: https://snyk.io/test/npm/swagger-egg
[download-image]: https://img.shields.io/npm/dm/swagger-egg.svg?style=flat-square
[download-url]: https://npmjs.org/package/swagger-egg

<!--
Description here.
-->
A egg swagger plugin for serving a [Swagger UI](https://swagger.io/tools/swagger-ui/), using [Swagger (OpenAPI v2)](https://swagger.io/specification/v2/) schemas automatically generated from your controller JSDoc comments.

**CAUTION**: Typescript is currently not support!

## Install

```bash
$ npm i swagger-egg --save
```

## Usage
Enable this plugin, visting `index.html` under `public directory` and you will get the Swagger UI page.

```js
// {app_root}/config/plugin.js
exports.swaggerEgg = {
  enable: true,
  package: "swagger-egg",
};
```

## Configuration
+ `swagger.info` is optional and will be generated from your application's `package.json` if not exist.
+ `swagger.tags` is required if controller's jsdoc comments used `tags`.

```js
// {app_root}/config/config.default.js
exports.swaggerEgg = {
  schema: {
    path: '/app/schema', // JSON Schema directory
  },
  swagger: {
    info: {
      title: 'Test swagger',
      description: 'Testing the Fastify swagger API',
      version: '0.1.0'
    },
    externalDocs: {
      url: 'https://swagger.io',
      description: 'Find more info here'
    },
    host: 'localhost',
    schemes: ['http', 'https'],
    consumes: ['application/json'],
    produces: ['application/json'],
    tags: [
      { name: 'user', description: 'User related end-points' },
      { name: 'admin', description: 'Admin related end-points' }
    ],
  },
};
```

see [config/config.default.js](config/config.default.js) for more detail.

## Grammer
### @function {Name}
The JSDoc `@function` is required, which is used to search router info from `app/router.js`.
```js
  /**
   * Function example
   *
   * @function index
   */
  async index() {
    this.ctx.body = 'hi, function example'
  }
```
### @description #tags {Tag1} {Tag2} ...
`#tags` is used for logical grouping of operations by resources or any other qualifier.

NOTE: Multiple tags should be separated by whitespace.
```js
  /**
   * Tags example
   *
   * @function index
   * @description #tags user admin
   */
  async index() {
    this.ctx.body = 'hi, tags example' 
  }
```
### @description #produces {Mimetype1} {Mimetype2} ...
`#produces` is used for declaring produces's mimetype.

NOTE: Multiple mimetypes should be separated by whitespace.
```js
  /**
   * Produces example
   *
   * @function index
   * @description #produces application/json
   */
  async index() {
    this.ctx.body = 'hi, produces example' 
  }
```

### @description #consumes {Mimetype1} {Mimetype1} ...

`#consumes` is used for declaring consumes's mimetype.

NOTE: Multiple mimetypes should be separated by whitespace.
```js
  /**
   * Consumes example
   *
   * @function index
   * @description #consumes application/json
   */
  async index() {
    this.ctx.body = 'hi, consumes example' 
  }
```

### @description #parameters {PrameterName} {In} {parameterSchema} {Required} - {Description}
`#parameters` is used for declaring api request parameters.

NOTE: Description is separated by ` - ` and others are separated by whitespace.
NOTE: `Required` is boolean type.
```js
  /**
   * Response example
   *
   * @function index
   * @description #parameters in body schema.user true - new body parameter
   */
  async index() {
    this.ctx.body = 'hi, response example' 
  }
```

### #responses {HttpStatus} {ResponseSchema} - {Description}

`#responses` is used for declaring api response info.

NOTE: Description is separated by ` - ` and others are separated by whitespace.

```js
  /**
   * Parameters example
   *
   * @function index
   * @description #parameters in body schema.user true - new body parameter
   */
  async index() {
    this.ctx.body = 'hi, parameters example' 
  }
```

## Example

```js
// {app_root}/app/controller/users.js

const Controller = require('egg').Controller;

class UserController extends Controller {

  /**
   * Index action #swagger-api
   *
   * @function index
   * @memberof UserController
   * @description #tags user
   * @description #produces application/json
   * @description #parameters index query schema.definitions.id true - index query parameter
   * @description #responses 200 schema.user - index response
   */
  async index() {
    this.ctx.body = 'hi, index action' + this.app.plugins.swaggerEgg.name;
  }

  /**
   * New action #swagger-api
   *
   * @function new
   * @memberof UserController
   * @description #tags user
   * @description #consumes application/x-www-form-urlencoded
   * @description #produces application/json
   * @description #parameters in body schema.user true - new body parameter
   * @description #responses 200 schema.user - new response
   */
  async new() {
    this.ctx.body = 'hi, new action' + this.app.plugins.swaggerEgg.name;
  }

  /**
   * Show action #swagger-api
   *
   * @function show
   * @memberof UserController
   * @description #tags user
   * @description #produces application/json
   * @description #parameters id path schema.definitions.id true - show path parameter
   * @description #responses 200 schema.user - show response
   */
  async show() {
    this.ctx.body = 'hi, show action' + this.app.plugins.swaggerEgg.name;
  }

  /**
   * Edit action #swagger-api
   *
   * @function edit
   * @memberof UserController
   * @description #tags user
   * @description #consumes application/x-www-form-urlencoded
   * @description #produces application/json
   * @description #parameters id path schema.definitions.id true - edit path parameter
   * @description #parameters in body schema.user true - edit body parameter
   * @description #responses 200 schema.user - edit response
   */
  async edit() {
    this.ctx.body = 'hi, edit action ' + this.app.plugins.swaggerEgg.name;
  }

  /**
   * Create action #swagger-api
   *
   * @function create
   * @memberof UserController
   * @description #tags user
   * @description #consumes application/x-www-form-urlencoded
   * @description #consumes application/json
   * @description #parameters in body schema.user true - create body parameter
   * @description #responses 200 schema.user - create response
   */
  async create() {
    this.ctx.body = 'hi, create action ' + this.app.plugins.swaggerEgg.name;
  }

  /**
   * Update action #swagger-api
   *
   * @function update
   * @memberof UserController
   * @description #tags user
   * @description #consumes application/x-www-form-urlencoded
   * @description #produces application/json
   * @description #parameters id path schema.definitions.id true - update path parameter
   * @description #parameters id body schema.user true - update body parameter
   * @description #responses 200 schema.user - update response
   */
  async update() {
    this.ctx.body = 'hi, update action ' + this.app.plugins.swaggerEgg.name;
  }

  /**
   * Destory action #swagger-api
   *
   * @function destory
   * @memberof UserController
   * @description #tags user
   * @description #consumes application/json
   * @description #produces application/json
   * @description #parameters id path schema.definitions.id false - destory path parameter
   * @description #responses 200 schema.user - destory response
   */
  async destory() {
    this.ctx.body = 'hi, destory action ' + this.app.plugins.swaggerEgg.name;
  }
}

module.exports = UserController;

```

## Questions & Suggestions

Please open an issue [here](https://github.com/JsonMa/swagger-egg/issues).

## License

[MIT](LICENSE)
