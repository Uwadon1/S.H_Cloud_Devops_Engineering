## MERN WEB STACK IMPLEMENTATION IN AWS


### Introduction

__The MERN stack is a popular choice for building dynamic web applications. It leverages a combination of JavaScript technologies:__

__MongoDB:__ A NoSQL document database for flexible data storage.

__Express.js:__ A lightweight web application framework for Node.js that simplifies building APIs and server-side part of a web applications.

__React.js:__ A powerful Javascript library for building interactive user interfaces based on components.

__Node.js:__ Node.js is an open-source, a Javascript cross-platform runtime environment for building fast and scalable server-side and networking applications.__

__This guide provides a comprehensive overview of setting up and utilizing each component of the MERN stack, to develop robust web applications.__


## Step 0: Prerequisites

__1.__ Launched an Ubuntu EC2 Instance named “Mern_Stack_Server” with AMI of 24.04 LTS (HVM) in the us-west-2 region using the AWS console.

![Create Server Name](./img/server-name.png)

__2.__ Gave it the instance type of t3.small. 

The choice of the instance type was based on the following:

- __Memory:__ The t3.small instance offers more memory than the t2.micro, which is advantageous for applications that require more memory to operate efficiently.

- __Burst Capability:__ While both instances offer burstable CPU performance, the t3 instances have a more flexible burst model, allowing for more sustained performance during burst periods. This is important for workloads that require consistent performance over longer periods.

- __Performance:__ While both instances offer burstable performance, the t3.small typically provides better baseline performance compared to the t2.micro. This might be necessary for applications that require a bit more processing power.


Created SSH key pair named __mern_stack__ to access the instance on port 22.  The default VPC and Subnet were used for the networking configuration.

![Instance Type](./img/t3-small.png)



__3.__ The security group was configured with the following inbound rules:

- Allow traffic on port 22 (SSH) with source from any IP address. 
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
- Allow traffic on port 80 (HTTP) with source from anywhere on the internet. (All these would be set by default, later we would go into the security group to set the remaining 2 inbound rules manually.)
*- Allow traffic on port 5000 (Custom TCP) with source from anywhere.
*- Allow traffic on port 3000 (Custom TCP) with source from anywhere.

We will leave the storage at default; 8gb gp3 volume storage. Then we hit the launch instance button.

![Security Rules](./img/security-rules.png)

![Security Rules 2](./img/security-rules2.png)


__4.__ First, we need to move the keypair file from the download folder into the .ssh folder; 
 cp ~/Downloads/mern_stack.pem ~/.ssh/

The private ssh key that got downloaded has now been moved, permission was changed for the private key file and then used to connect to the instance by running the following commands;

```
chmod 400 ~/.ssh/mern_stack.pem
```

```
ssh -i ~/.ssh/mern_stack.pem ubuntu@18.236.197.116
```
Where __username=ubuntu__ and __public ip address=18.236.197.116__

![Connect to instance](./img/ssh-instance.png)




## Step 1 - Backend Configuration

__1.__ __Update and upgrade the server’s package index__

Run a sudo apt update to download package information from all configured sources.
```
sudo apt update
```

![Update Packages](./img/apt-update.png)


Run a sudo apt upgrade to upgrade the package
```
sudo apt upgrade -y
``
![Upgrade Packages](./img/apt-upgrade.png)



__2.__ __Get the location of Node.js software from ubuntu repositories__.

Next, will be to install Node.js, first we get the location of node.js from the Ubuntu repository using the following command. 

```bash
curl fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
![Nodejs repository](./img/nodejs-repo.png)

__3.__ __Install node.js with the command below__.

```bash
sudo apt-get install nodejs -y
```
![Install nodejs](./img/install-nodejs.png)

__Note:__ the above command installs both node.js and npm. NPM is a package manager for Node just as apt is a package manager for Ubuntu. It is used to install Node modules and packages and to manage dependency conflicts.

__4.__ __Verify the Node installation with the command below__.

```bash
node -v        // Gives the node version

npm -v        // Gives the node package manager version
```
![node-npm version](./img/node-npmv.png)


### Application Code Setup

__1.__ __Create a new directory for the TO-DO project and switch to it. Then initialize the project directory.__

We then create a directory that will house our codes and packages and all subdirectories to represent components of our application.

```
mkdir todo && ls && cd Todo
```
![Create Directory](./img/mkdir.png)


Inside this directory we will instantiate our project using npm init. This enables Javascript to install packages useful for spinning up our application.

```
npm init
```

![Project dir](./img/npm-init.png)

This is to initialize the project directory and in the process, create a new file called package.json. This file will contain information about your application and the dependencies it needs to run.
Follow the prompts after running the command. You can press “Enter” several times to accept default values, then accept to write out the package.json file by typing yes.


### Install ExpressJs

Express is a framework for Node.js. It simplifies development and abstracts a lot of low-level details. For example, express helps to define routes of your application based on HTTP methods and URLs.

__1.__ __Install Express using npm__

We will be installing express which is Nodejs framework and will be helpful when creating routes for our application via HTTP requests.

```bash
npm install express
```
![Install express](./img/install-express.png)

__2.__ __Create a file index.js and run ls to confirm the file__

Create an index.js file which will contain code useful for spinning up our express server

```bash
touch index.js && ls
```

__3.__ __Install dotenv module__

Install the dotenv module which is a module that loads environment variables from a .env file into process.env. The .env files are useful for hiding important credentials which shouldn’t be exposed.


```bash
npm install dotenv
```
![dotenv](./img/install-dotenv.png)

__4.__ __Open index.js file__

Inside the index.js file we type the following code as seen below.

```bash
vim index.js
```
Type the code below into it

```bash
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});

app.use((req, res, next) => {
  res.send('Welcome to Express');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```
![index.js code](./img/vim-indexjs.png)

__Note:__ Port 5000 has been specified to be used in the code. This was required later on the browser.

__5.__ __Start the server to see if it works. Open your terminal in the same directory as your index.js file. Run__
```bash
node index.js
```
![Start server](./img/start-server.png)

Port 5000 has been opened in ec2 security group.

__Access the server with the public IP followed by the port__

```bash
http://18.236.197.116:5000
```
![Express page](./img/express-page.png)


## Routes

We will create a routes folder which will contain code pointing to the three main endpoints used in a todo application. This will contain the post, get and delete requests which will be helpful when interacting with our client-side and database via restful apis.

There are three actions that the ToDo application needs to be able to do:
- Create a new task
- Display list of all task
- Delete a completed task

Each task was associated with some particular endpoint and used different standard __HTTP__ request methods: __POST__, __GET__, __DELETE__.


__1.__ __Create a folder named routes, switch to routes directory and create a file api.js. 

For each task, routes were created which defined various endpoints that the ToDo app depends on.

Write the below code in api.js. It is an example of a simple route that fires various endpoints.

Open the file__
```bash
mkdir routes
cd routes
touch api.js
```
![Router](./img/create-router.png)

__Copy__ the code below into the api.js file.

```bash
const express = require('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

});

module.exports = router;
```
![Route](./img/vim-routes.png)


## Models

A model is at the heart of JavaScript based applications and it is what makes it interactive.

Models are used to define the database schema. This is important in order to define the fields stored in each MongoDB document.

In essence, the schema is a blueprint of how the database is constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties.
To create a schema and a model, mongoose was installed, which is a Node.js package that makes working with MongoDB easier.

__1.__ __Change the directory back to Todo folder and install mongoose__

We will be creating the models directory which will be used to define our database schema. A Schema is a blueprint of how our database will be structured which includes other fields which may not be required to be stored in the database.
Inside the todo directory, run npm install mongoose to install mongoose.

```bash
npm install mongoose
```
![Mongoose](./img/install-mongoose.png)

__2.__ __Create a new folder called models, switch to models directory, create a file todo.js inside models. 

Create a models directory and then create a file in it todo.js Write the below code inside the todo.js file

Open the file__

```bash
mkdir models && cd models && touch todo.js
```
![Create New Model Directory](./img/mkdir-models.png)

Paste the code below into the file

```bash
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

// Create schema for todo
const TodoSchema = new Schema({
  action: {
    type: String,
    required: [true, 'The todo text field is required']
  }
});

// Create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```
![Schema](./img/schema.png)

The api.js file in the ‘routes’ directory is now updated to make use of the new model.

__3.__ __In Routes directory, open api.js and delete the code inside __.





We have to navigate to the api.js file inside the route directory

![Navigate to the Router folder](./img/nav-apijs.png)


```bash
vim api.js
```
Type :%d to delete the previous codes inside this file and then paste the new code below into it

```bash
const express = require('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {
  // This will return all the data, exposing only the id and action field to the client
  Todo.find({}, 'action')
    .then(data => res.json(data))
    .catch(next);
});

router.post('/todos', (req, res, next) => {
  if (req.body.action) {
    Todo.create(req.body)
      .then(data => res.json(data))
      .catch(next);
  } else {
    res.json({
      error: "The input field is empty"
    });
  }
});

router.delete('/todos/:id', (req, res, next) => {
  Todo.findOneAndDelete({"_id": req.params.id})
    .then(data => res.json(data))
    .catch(next);
});

module.exports = router;
```
![Router update](./img/update-routes.png)


## MongoDB Database

We will need a database to store all information when we make a post request to an endpoint. We will be using mLab.

__mLab__ provides MongoDB database as a service solution (DBaaS). MongoDB has two cloud database management system components: mLab and Atlas, Both were formerly cloud databases managed by MongoDB (MongoDB acquired mLab in 2018, with certain differences). In November, MongoDB merged the two cloud databases and as such, __mLab.com__ redirects to the __MongoDB Atlas website__.

__1.__ __Create a MongoDB database and collection inside mLab__

MongoDB Cluster Overview

![DB overview](./img/create-cluster1.png)

AWS cloud provider, in region Oregon (us-west-2) was selected.

![Cloud Selection overview](./img/create-cluster2.png)

Access from anywhere to the MongoDB database was allowed (Not secure but it is ideal for testing).

![Network access](./img/network-access.png)

A __database__ named __todo_db__ and __collections__ named __todos__ was created.

![Todo DB](./img/create-dbcolls.png)


__2.__ __Create a file in your Todo directory and name it .env, open the file__

```bash
touch .env

vim .env
```
To connect mongoose(application_db) to our database service we connect to it using the connection credential provided by mLab

Add connection string below to access the database: To get the connection string, click on the project overview page and click on the connection string button below.

```bash
DB = ‘mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority’

mongodb+srv://<db_username>:<db_password>@databases.jjhoghe.mongodb.net/?retryWrites=true&w=majority&appName=DATABASES

E.g:
mongodb+srv://Uwadon1:Admin12345@databases.jjhoghe.mongodb.net/?retryWrites=true&w=majority&appName=DATABASES 
```

Change the db username and db_password to the one specified by you when creating the database and collection.

![Connection string](./img/db-constrings.png)


__3.__ __Update the index.js to reflect the use of .env so that Node.js can connect to the database__.

```bash
vim index.js
```

Delete existing content in the file, and update it with the entire code below:

```bash
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

// Connect to the database
mongoose.connect(process.env.DB)
  .then(() => console.log(`Database connected successfully`))
  .catch(err => console.log(err));

// Since mongoose promise is deprecated, we override it with Node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
  console.log(err);
  next();
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});

```
![index.js](./img/update-indexjs.png)

Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

__4.__ __Start your server using the command__

Then we run node index.js to test our connection.

```bash
node index.js
```
![Server](./img/warning.png)

There was a deprecation warning as displayed in the image above.
To silent this warning, __{ useNewUrlParser: true, useUnifiedTopology: true }__ was removed from the code.


## Testing Backend Code without Frontend using RESTful API

Postman was used to test the backend code.
The endpoints were tested. For the endpoints that require body, JSON was sent back with the necessary fields since it’s what was set up in the code.

__1.__ __Open Postman and Set the header__

```bash
http://18.236.197.116:5000/api/todos
```
![Post request](./img/postman-header.png)

### Create POST requests to the API

Input the following: 
{
    "action": "Complete the MERN_STACK project proposal"
}

![Post request](./img/post-reg1.png)

{
    "action": "Start the MERN_STACK project now!"
}

![Post request 2](./img/post-reg2.png)


{
    "action": "Prepare the Documentation!"
}

![Post request 3](./img/post-reg3.png)


{
    "action": "This is the final database for now!"
}


![Post request 4](./img/post-reg4.png)


### Check Database Collections

![DB Collections](./img/db-collections.png)


### Make a GET requests to the API

This request retrieves all existing records from our To-Do application (backend requests these records from the database and sends us back as a response to GET request).

![Get request](./img/get-req1.png)


### Create a DELETE request to the API

To delete any of the data we just inserted in the post endpoint, we need to copy any of the id number and paste on the delete function after the ip/todo url: 680212d996ebfef5fce6bc6a

![Delete request](./img/del-req.png)


###  Check Database Collections

![DB Collectioms](./img/db-collections2.png)



## Step 2 - Frontend Creation


It is time to create a user interface for a Web client (browser) to interact with the application via API

__1.__ __In the todo directory which is same root directory containing the backend code.
Run `npx create-react-app client’. This creates a client directory containing the necessary packages required for react to work.

```bash
npx create-react-app client
```
![React](./img/setup-react.png)

This created a new folder in the Todo directory called client, where all the react code was added.

### Running a React App

Before testing the react app, the following dependencies needs to be installed in the project root directory.

- __Install concurrently__. It is used to run more than one command simultaneously from the same terminal window.
```bash
npm install concurrently --save-dev
```
![Concurrent](./img/install-concurrent.png)


- __Install nodemon__. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
```bash
npm install nodemon --save-dev
```
![Nodemon](./img/install-nodemon.png)


- In Todo folder open the package.json file, change the highlighted part of script line in the below screenshot and replace with the code below:

```bash
"scripts": {
  "start": "node index.js",
  "start-watch": "nodemon index.js",
  "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
}
```
![Package.json](./img/packagejson.update.png)

### Configure Proxy In package.json

- Change directory to “client”
```bash
cd client
```

- Open the package.json file
```bash
vim package.json
```
![Package](./img/open-pkgclient.png)

Add the key value pair in the package.json file
```bash
“proxy”: “http://localhost:5000”
```
![Proxy](./img/proxy.png)

The whole purpose of adding the proxy configuration above is to make it possible to access the application directly from the browser by simply calling the server url like
http://locathost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Ensure you are inside the Todo directory, and simply do:
```bash
npm run dev
```
![](./imgruning-app.png)
The app opened and started running on localhost:3000

__Note__: In order to access the application from the internet, TCP port 3000 had been opened on EC2.


## Creating React Components

One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For the Todo app, there are two stateful components and one stateless component. From Todo directory, run:

```bash
cd client
```

Move to the “src” directory
```bash
cd src
```

__2.__ __Inside your src folder, create another folder called “components”__

```bash
mkdir components
```
Move into the components directory
```bash
cd components
```
![Component](./img/create-components.png)

__3.__ __Inside the ‘components’ directory, create three files “Input.js”, “ListTodo.js” and 

“Todo.js”.__
```bash
touch Input.js ListTodo.js Todo.js
```
![Component files](./img/components-file.png)

#### Open Input.js file
```bash
vim Input.js
```
Paste in the following:

```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {
  state = {
    action: ""
  }

  handleChange = (event) => {
    this.setState({ action: event.target.value });
  }

  addTodo = () => {
    const task = { action: this.state.action };

    if (task.action && task.action.length > 0) {
      axios.post('/api/todos', task)
        .then(res => {
          if (res.data) {
            this.props.getTodos();
            this.setState({ action: "" });
          }
        })
        .catch(err => console.log(err));
    } else {
      console.log('Input field required');
    }
  }

  render() {
    let { action } = this.state;
    return (
      <div>
        <input type="text" onChange={this.handleChange} value={action} />
        <button onClick={this.addTodo}>add todo</button>
      </div>
    );
  }
}

export default Input;
```
![Input](./img/input.png)

In oder to make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

Move to the client folder
```bash
cd ../..
```
__Install Axios__
```bash
npm install axios
```
![Install Axios](./img/install-axios.png)

#### Go to components directory
```bash
cd src/components
```

#### After that open the ListTodo.js

```bash
vim ListTodo.js
```
Copy and paste the following code:

```bash
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {
  return (
    <ul>
      {
        todos && todos.length > 0 ? (
          todos.map(todo => {
            return (
              <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
                {todo.action}
              </li>
            );
          })
        ) : (
          <li>No todo(s) left</li>
        )
      }
    </ul>
  );
}

export default ListTodo;
```
![Todo list](./img/list-todo.png)

#### Then in the Todo.js file, write the following code

```bash
vim Todo.js
```

```bash
import React, { Component } from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {
  state = {
    todos: []
  }

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios.get('/api/todos')
      .then(res => {
        if (res.data) {
          this.setState({
            todos: res.data
          });
        }
      })
      .catch(err => console.log(err));
  }

  deleteTodo = (id) => {
    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch(err => console.log(err));
  }

  render() {
    let { todos } = this.state;
    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
      </div>
    );
  }
}

export default Todo;
```
![Todo](./img/todo-js.png)

__We need to make a little adjustment to our react code. Delete the logo and adjust our App.js to look like this__

### Move to src folder
```bash
cd ..
```

Ensure to be in the src folder and run:
```bash
vim App.js
```

#### Copy and paste the following code

```bash
import React from 'react';
import Todo from './components/Todo';
import './App.css';

const App = () => {
  return (
    <div className="App">
      <Todo />
    </div>
  );
}

export default App;

```
![Appjs](./img/app-js.png)

####  In the src directory, open the App.css

```bash
vim App.css
```

Paste the following code into it

```bash
.App {
  text-align: center;
  font-size: calc(10px + 2vmin);
  width: 60%;
  margin-left: auto;
  margin-right: auto;
}

input {
  height: 40px;
  width: 50%;
  border: none;
  border-bottom: 2px #101113 solid;
  background: none;
  font-size: 1.5rem;
  color: #787a80;
}

input:focus {
  outline: none;
}

button {
  width: 25%;
  height: 45px;
  border: none;
  margin-left: 10px;
  font-size: 25px;
  background: #101113;
  border-radius: 5px;
  color: #787a80;
  cursor: pointer;
}

button:focus {
  outline: none;
}

ul {
  list-style: none;
  text-align: left;
  padding: 15px;
  background: #171a1f;
  border-radius: 5px;
}

li {
  padding: 15px;
  font-size: 1.5rem;
  margin-bottom: 15px;
  background: #282c34;
  border-radius: 5px;
  overflow-wrap: break-word;
  cursor: pointer;
}

@media only screen and (min-width: 300px) {
  .App {
    width: 80%;
  }

  input {
    width: 100%;
  }

  button {
    width: 100%;
    margin-top: 15px;
    margin-left: 0;
  }
}

@media only screen and (min-width: 640px) {
  .App {
    width: 60%;
  }

  input {
    width: 50%;
  }

  button {
    width: 30%;
    margin-left: 10px;
    margin-top: 0;
  }
}

```
![App css](./img/app-css.png)

#### In the src directory, open the index.css

```bash
vim index.css
```

#### Copy and paste the code below:

```bash
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  background-color: #282c34;
  color: #787a80;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New", monospace;
}
```
![index css](./img/index-css.png)

#### Go to the Todo directory
```bash
cd ../..
```

Run:
```bash
npm run dev
```
![dev](./img/run-dev.png)

At this point, the To-Do app is ready and fully functional with the functionality discussed earlier: Creating a task, deleting a task, and viewing all the tasks.

__The client can now be viewed in the browser__

![Todo web](./img/todo-website.png)

__Add some todos via the browser .__

![Todos](./img/add-todos.png)

__Check them on the MongoDB database__

![Todo](./img/add-todo-db.png)

### Conclusion

By following this documentation and leveraging the provided resources, one is well-equipped to build and deploy full-fledged web applications utilizing the MERN stack.
