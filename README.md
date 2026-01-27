# Video Conferencing App

P2P Architecture 
Soon.

## Bakcend Setup
mkdir backend
npm init
npm i express mongoose bcrypt http-status socket.io cors crypto
npm i -D nodemon 

Create mongosdb project
IP Address 0.0.0.0/0 (not for production)

Database Access (for credentials)

backend
  src 
     controllers
     routes
     models
     app.js

package.json
type: module

app.js
```js
import express from 'express';
import {creteServer} from 'node:http'; // connect socket server with express instance

import {Server} from 'socket.io';

import mongoose from 'mongoose';
import cors from 'cors';

const app = express();


app.get('/', (req, res) => {
    return res.json({hello:"world"});
});

const start = async() => {
    app.listen(8000, () => {
        console.log('listening on port 8000')
    });
};

start();
```

package.json
scripts
dev: nodemon src/app.js 
start: node src/app.js
prod: pm2 src/app.js

npm run dev

socket.io setup
```js
// const app = express();
const server = createServer(app);
const io = new Server(server);

```

```js
app.set("port", (process.end.PORT || 8000))
```

```js
const start = async() => {
    const connectionDb = await mongoose.connect('atlas-url')

    console.log('MONGO connection DB HOST: ${connectionDb.connection.host}')
    server.listen(app.get("port"), () => {
        console.log('listening on port 8000')
    });
};
```

cd controllers 
touch socketManager.js
```js
import {Server} from 'socket.io'

export const connectToSocker = (server) => {
    const io = new Server(server);
    return io;
}
```

app.js
```js
import {connectToSocket} from './controllers/sockerManager.js'
const io = connectToSocker(server);
```

app.js
```js
app.use(cors());
app.use(express.json({limit: "40kb"}));
app.use(express.urlencoded({ limit: "40kb", extended: true}));


```

routes/users.routes.js
```js
import { Router} from 'express';

const router = Router();

router.route('/login');
router.route('/register')
router.route('/add_to_activity')
router.route('/get_all_activity')

export router;
```

## Creating DB Models
models/users.models.js
```js
const {Schema} from 'mongoose'

const userSchema = new Schema({
    name: String,
    username: String,
    password: String,
    token: String
});

const User = mongoose.model("User", userSchema);
export {User};
```

meeting.model.js
```js
user_id: String
meetingCode: String
date: Date.now type Date

Meeting 
```

## Authentication Routes
controllers/user.controller.js
```js
import bcrypt , {hash} from 'bcrypt';
import User
import httpStatus 

const register = async (req, res) => {
    cosnt {name, username, password} = req.body;

    try{
        const existingUser = await User.findOne({username});

        if(existingUser) {
            return res.status(httpStatus.FOUND).json({msg: "user already exists"});

            const hashedPassword = await bcrypt.hash(password, 10);

            const newUser = new User({
                name,
                username,
                password: hashedPassword
            });

            await newUser.save();

            res.status(httpStatus.CREATED).json({msg: "user registered"});
        } 
    } catch (e) {
        res.json({msg: `Something went wrong ${e}`})
    }
}
```

login functionality

contorllers/user.controller.js
```js
const login = async(req, res) => {
    const {username, password} = req.body;

    if(!username || !password) {
        return res.status(400).json({msg: "please provide"});

    }
    try {
        const user = await User.find({username});
        if(!user) {
            return res.status(httpStatus.NOT_FOUND).json({msg: "user nto found"});
        }

        if(bcrypt.compare(password, user.password)) {
            let token = crypto.randomBytes(20).toSting("hex");

            user.token = token;
            await user.save();

             return res.status(httpStatus.OK).json({token: token});
        }
    }catch (e) {
        return res.status(500).json({msg: "something went wrong"});
    }
};

export {login, register}
```

routes/userController.js
```js
import { Router} from 'express';

const router = Router();

router.route('/login').post(login)
router.route('/register').post(register)
router.route('/add_to_activity')
router.route('/get_all_activity')

export router;
```

app.js
```js
import userRoutese from './routes/userRoutese.js'

app.use("/api/v1/users", userRoutes);
```

## Sockets for live interection
Follow the link to first understand how socket.io work by creating a basic real-time chat applicaiton in node. [socket.io](https://socket.io/docs/v4/tutorial/introduction)
```diff 
import express from 'express';
import { createServer } from 'node:http';
+ import { fileURLToPath } from 'node:url';
+ import { dirname, join } from 'node:path';

+const app = express();
+const server = createServer(app);
+
+const __dirname = dirname(fileURLToPath(import.meta.url));

app.get('/', (req, res) => {
  res.sendFile(join(__dirname, 'index.html'));
});

server.listen(3000, () => {
  console.log('server running at http://localhost:3000');
});
```

controllers sockerManager.js
```js

```
