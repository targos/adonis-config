<img src="https://res.cloudinary.com/adonisjs/image/upload/q_100/v1557762307/poppinss_iftxlt.jpg" max-width="600px">

# Config
[![circleci-image]][circleci-url] [![npm-image]][npm-url] ![](https://img.shields.io/badge/Typescript-294E80.svg?style=for-the-badge&logo=typescript)

Extremely simple module to **decouple application config** from the file system, which has handful of benefits.

1. Can rely on more sources to feed configuration.
2. Easy to define fake values during testing.
3. A much nicer API to read nested values.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
## Table of contents

- [Usage](#usage)
- [Why not simply create the config files?](#why-not-simply-create-the-config-files)
- [Multiple config sources](#multiple-config-sources)
- [Easy to fake during tests](#easy-to-fake-during-tests)
    - [Config file](#config-file)
    - [Config module](#config-module)
    - [Application code](#application-code)
    - [Test code](#test-code)
- [Reading nested values](#reading-nested-values)
- [Change log](#change-log)
- [Contributing](#contributing)
- [Authors & License](#authors--license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Usage
Install the package from npm as follows

```sh
npm i @poppinss/config

# yarn
yarn add @poppinss/config
```

and then use the class as follows:

```ts
import { Config } from '@poppinss/config'

const initialConfiguration = {
  app: {
    name: 'adonis',
  },
  database: {
    connection: 'mysql',
  },
  logger: {
    level: 'debug',
  },
}

const config = new Config(initialConfiguration)

config.get('app.name') // adonis
config.get('database.connection') // mysql
config.get('database.user', 'root') // root
```

## Why not simply create the config files?
Majority of projects create config files next to the source files or inside a dedicated config directory and require those files wherever needed.

However, with AdonisJs, we make the process of config management a little bit better over manually requiring config files and it has handful of benefits.

AdonisJs recommends to save all configuration inside the `config` directory and then behind the scenes it read those files and feed it's content to the `Config` class and later the application developer can get rid of importing config files and rely on the `Config` class instance instead.

## Multiple config sources
We virtually decouple the config from the filesystem, which means your app can read the configuration from anywhere and pass it to the `Config` class. For example:

```ts
const { db } from 'some-db-module'
import { Config } from '@poppinss/config'

const settings = await db.table('settings').select('*')

const config = new Config({}) // start with empty store
settings.forEach((row) => {
  config.set(row.key, JSON.parse(row.value))
})
```

## Easy to fake during tests
Now since, you are not requiring the config files directly inside your application code, you can easily provide fake values during tests.

#### Config file
```ts
export const db = {
  connection: 'pg'
}
```

#### Config module
```ts
import { Config } from '@poppinss/config'
import { db } from './config/database'

export default new Config({ db })
```

#### Application code
```ts
import { Db } from 'some-db-module'
import config from './config'

const db = new Db(config.get('db'))

class UserController {
  async store () {
    // perform insert
  }
}
```

#### Test code
```ts
import config from './config'
config.set('db.connection', 'sqlite')

// now run tests and connection will be sqlite over pg
```

## Reading nested values
Reading nested values in Javascript isn't fun. You have to ensure that top level object is actually an object before accessing it's child.

However, with this module, you can pull nested values without worrying about the intermediate parents being `undefined` or `null`.

```ts
config.get('database.mysql.connection.host', '127.0.0.1')
```

The `get` method will return `127.0.0.1` if any of the parents or the value of `host` itself is non-existent.

## Change log

The change log can be found in the [CHANGELOG.md](CHANGELOG.md) file.

## Contributing

Everyone is welcome to contribute. Please go through the following guides, before getting started.

1. [Contributing](https://adonisjs.com/contributing)
2. [Code of conduct](https://adonisjs.com/code-of-conduct)


## Authors & License
[Harminder virk](https://github.com/Harminder virk) and [contributors](https://github.com/poppinss/config/graphs/contributors).

MIT License, see the included [MIT](LICENSE.md) file.

[circleci-image]: https://img.shields.io/circleci/project/github/poppinss/config/master.svg?style=for-the-badge&logo=circleci
[circleci-url]: https://circleci.com/gh/poppinss/config "circleci"

[npm-image]: https://img.shields.io/npm/v/@poppinss/config.svg?style=for-the-badge&logo=npm
[npm-url]: https://npmjs.org/package/@poppinss/config "npm"
