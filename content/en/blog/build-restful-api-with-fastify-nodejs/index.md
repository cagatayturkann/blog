---
title: 'Build a RESTful API with Fastify and Node.js'
# subtitle: "Blog post subtitle :zap:"
summary: Fastify is a modern web framework for Node.js that focuses on providing high performance with low overhead. In this article, I will explain in detail what Fastify is, how it's used, and best practice recommendations.
date: 2025-02-07  
cardimage: '/en/blog/build-restful-api-with-fastify-nodejs/images/cover.png'
featureimage: 'en/blog/build-restful-api-with-fastify-nodejs/images/cover.png'
featured: true
# caption: Image caption
categories: ['Node.js']
tags: ['nodejs', 'fastify']
authors:
  - Çağatay: /images/author.jpeg
---

Hello everyone! In this article, I'm going to talk about Fastify, a fast and low overhead web framework for Node.js. We'll build a simple TODO API together, and I'll explain how Fastify's features can make your development process more efficient.

You can access all the source code for this project on GitHub: [fastify-nodejs-restful-api](https://github.com/cagatayturkann/blogProjects/tree/main/fastify-nodejs-restful-api)

### What is Fastify and Why Should We Use It?

Fastify is a modern web framework for Node.js that focuses on providing high performance with low overhead. While most Node.js developers are familiar with Express.js, Fastify offers some compelling advantages:
- Up to 2x faster than Express
- Built-in schema validation
- Automatic Swagger documentation
- Plugin-based architecture

### Setting Up Our Project

Let's start by setting up our project. First, we need to install some dependencies. Create a new directory and run these commands:

```bash
mkdir fastify-todo-api
cd fastify-todo-api
npm init -y
```

Now, let's install the packages we'll need:

```json
{
  "dependencies": {
    "fastify": "^3.29.0",
    "fastify-swagger": "^5.2.0",
    "uuid": "^8.3.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.16"
  }
}
```

### Project Structure

Before we start coding, let's organize our project structure. We'll follow a clean and maintainable approach:

```
fastify-todo-api/
├── app.js           # Main server file
├── items.js         # Our data store
├── routes/
│   └── todoRouter.js    # Route definitions
└── controllers/
    └── todoController.js # Request handlers
```

### Creating Our First Fastify Server

Let's start with our main server file (`app.js`). Here's how we set up a basic Fastify server:

```javascript
const fastify = require('fastify')({ logger: true });

// Setting up Swagger documentation
fastify.register(require('fastify-swagger'), {
  exposeRoute: true,
  routePrefix: '/docs',
  swagger: {
    info: { title: 'fastify-api' },
  },
});

// Registering our routes
fastify.register(require('./routes/todoRouter'));

const PORT = 5000;
const start = async () => {
  try {
    await fastify.listen(PORT);
  } catch (error) {
    fastify.log.error(error);
    process.exit(1);
  }
};

start();
```

What's happening in this code?
- We create a Fastify instance with logging enabled
- We set up Swagger documentation (accessible at /docs)
- We register our routes using Fastify's plugin system
- We start the server on port 5000

### Understanding Fastify's Schema Validation

One of Fastify's most powerful features is its schema validation system. Let's look at how we can use it in our `todoRouter.js`:

```javascript
// First, we define what a TODO item looks like
const Item = {
  type: 'object',
  properties: {
    id: { type: 'string' },
    name: { type: 'string' },
  },
};

// Then we create schemas for our endpoints
const getItemsOpts = {
  schema: {
    response: {
      200: {
        type: 'array',
        items: Item,
      },
    },
  },
  handler: getItems,
};
```

What makes this special?
- Fastify automatically validates all incoming and outgoing data
- It generates Swagger documentation from these schemas
- It improves performance by optimizing serialization
- It catches errors before they reach your handlers

### Creating Our Controllers

Now let's look at how we handle requests in `todoController.js`:

```javascript
let items = require('../items');
const { v4: uuidv4 } = require('uuid');

// Get all items
const getItems = (req, reply) => {
  reply.send(items);
};

// Create new item
const addItem = (req, reply) => {
  const { name } = req.body;
  const item = {
    id: uuidv4(),
    name,
  };
  items = [...items, item];
  reply.code(201).send(item);
};
```

Notice how Fastify makes response handling simple:
- No need to set Content-Type headers manually
- Method chaining for status codes and sending responses
- Automatic response serialization

### Testing Our API

Now that we have everything set up, let's test our API. You can use curl or any API testing tool:

```bash
# Get all items
curl http://localhost:5000/items

# Create a new item
curl -X POST \
  http://localhost:5000/items \
  -H 'Content-Type: application/json' \
  -d '{"name":"Learn Fastify"}'
```

### Performance Features You Should Know About

Fastify isn't just fast by accident. Here's why it performs so well:

1. **Schema-based Serialization**
   - Pre-compiles schemas for faster validation
   - Optimizes JSON serialization
   - Reduces processing overhead

2. **Efficient Routing**
   - Uses a radix tree for route matching
   - Faster than regex-based routing
   - Optimized parameter handling

3. **Lightweight Core**
   - Minimal baseline overhead
   - Plugin system for adding features
   - Efficient memory usage

### Best Practices I Recommend

After working with Fastify, here are some practices I've found helpful:

1. **Always Use Schemas**
   ```javascript
   fastify.get('/items', {
     schema: {
       response: {
         200: itemSchema
       }
     }
   })
   ```

2. **Organize with Plugins**
   ```javascript
   fastify.register(require('./routes/items'))
   fastify.register(require('./routes/users'))
   ```

3. **Handle Errors Properly**
   ```javascript
   fastify.setErrorHandler(function (error, request, reply) {
     reply.status(error.statusCode || 500).send({error: error.message})
   })
   ```

### Conclusion

Fastify provides an excellent foundation for building high-performance Node.js APIs. Its built-in features like schema validation and swagger documentation make it a great choice for modern web applications. In future articles, I'll explore more advanced Fastify features and how to use them effectively in production. Thanks for reading!

Remember to check out the [Fastify documentation](https://www.fastify.io/docs/latest/) for more detailed information about all these features.