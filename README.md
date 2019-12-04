### 1. Initialization
Name express server here:
```
mkdir EXPRESS-SERVER-NAME && cd $_ &&
```
Run rest of commands:
```
echo "node_modules" > .gitignore &&
npm init -y &&
npm i express morgan cors dotenv helmet &&
mkdir src &&
touch src/app.js &&
echo "NODE_ENV=development" > .env &&
echo 'PORT=8000' >> .env &&
echo "EXAMPLE='example-environmental-variable'" >> .env &&
echo ".env" >> .gitignore &&
touch src/server.js &&
npm i mocha chai supertest nodemon -D &&
mkdir test &&
touch test/app.spec.js &&
cp .env example.env &&
touch src/config.js &&
touch Procfile &&
echo "web: node src/server.js" > Procfile
```

### 2. Alter Files

package.json:

```
  "scripts": {
    "test": "mocha",
    "dev": "nodemon src/server.js",
    "start": "node src/server.js",
    "predeploy": "npm audit",
    "deploy": "git push heroku master"
  },

```

src/app.js:

```
'use strict';

const express = require('express')
const morgan = require('morgan')
const cors = require('cors')
const helmet = require('helmet')
const { NODE_ENV } = require('./config')

const app = express()

app.use(morgan((NODE_ENV === 'production') ? 'tiny' : 'common', {
  skip: () => NODE_ENV === 'test',
}))
app.use(cors())
app.use(helmet())

app.get('/', (req, res) => {
  res.send('Hello, world!')
})

app.use(function errorHandler(error, req, res, next) {
  let response
  if (NODE_ENV === 'production') {
    response = { error: 'server error' }
  } else {
    console.error(error)
    response = { error: error.message, details: error }
  }
  res.status(500).json(response)
})

module.exports = app
```

src/config.js:

```
'use strict';

module.exports = {
  PORT: process.env.PORT || 8000,
  NODE_ENV: process.env.NODE_ENV || 'development',
}
```

src/server.js:

```
'use strict';

require('dotenv').config()

const app = require('./app')
const { PORT } = require('./config')

app.listen(PORT, () => {
  console.log(`Server listening at http://localhost:${PORT}`)
})
```

test/app.spec.js:

```
'use strict';

const { expect } = require('chai')
const supertest = require('supertest')
const app = require('../src/app')

describe('App', () => {
  it('GET / responds with 200 "Hello, world!"', () => {
    return supertest(app)
      .get('/')
      .expect(200, 'Hello, world!')
  })
})
```

### 3. Reference commands

```
npm test
npm run dev
npm start
```
