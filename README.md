# think-mongoose

Wrap mongoose for ThinkJS 3.x

## Install 

```sh
npm install think-mongoose --save
```

## How to use

### Config extend

Change file `src/config/extend.js` (in multi module project, file is `src/common/config/extend.js`), add config: 

```js
const mongoose = require('think-mongoose');

module.exports = [
  mongoose
]
```

When add mongoose extend, it will add some below methods:

* `think.Mongoose` {Class} Base class, model class must be extends this class.
* `think.mongoose` {Function} get mongoose instance
* `ctx.mongoose` {Function} get mongoose instance, it's wrapped from think.mongoose
* `controller.mongoose` {Function} get mongoose instance, it's wrapped from think.mongoose

### Config adapter

Change file `src/config/adapter.js` (in multi module project, file is `src/common/config/adapter.js`), add config:

```js
exports.model = {
  type: 'mongoose',
  mongoose: {
    host: '127.0.0.1',
    user: '',
    password: '',
    database: 'test',
    options: {}
  }
}
```

or config connection string: 

```js
exports.model = {
  type: 'mongoose',
  mongoose: {
    connectionString: 'mongodb://user:pass@localhost:port/database',
    options: {
      config: { 
        autoIndex: false
      }
    }
  }
}
```

### Create model class

Create model class extends from think.Mongoose, like:

```js
// src/model/user.js

module.exports = class extends think.Mongoose {
  get schema() {
    return {
      name:    String,
      binary:  Buffer,
      living:  Boolean,
      updated: { type: Date, default: Date.now },
      age:     { type: Number, min: 18, max: 65 },
      mixed:   Schema.Types.Mixed,
      _someId: Schema.Types.ObjectId,
      array:      [],
      ofString:   [String],
      ofNumber:   [Number],
      ofDates:    [Date],
      ofBuffer:   [Buffer],
      ofBoolean:  [Boolean],
      ofMixed:    [think.Mongoose.Mixed],
      ofObjectId: [think.Mongoose.ObjectId],
      ofArrays:   [[]]
      ofArrayOfNumbers: [[Number]]
      nested: {
        stuff: { type: String, lowercase: true, trim: true }
      }
    }
  }
}
```

Sometime, you want to add some methods to schema, you can get schema instance by yourself, then return instance:

```js
module.exports = class extends think.Mongoose {
  get schema() {
    const schema = new think.Mongoose.Schema({ name: String, type: String });
    schema.methods.findSimilarTypes = function(cb) {
      return this.model('Animal').find({ type: this.type }, cb);
    };
    return schema;
  }
}
```

### Get model instance

You can get mongoose class instance by `think.mongoose`, `ctx.mongoose` or `controller.mongoose`.

```js
module.exports = class extends think.Controller {
  async indexAction() {
    const user = this.mongoose('user');
    const data = await user.find();
  }
}
```