# swagger-autogen

This module performs the automatic construction of the Swagger documentation. The module is able to identify the endpoints and automatically capture methods such as get, post, put, and so on. The module can also identify the paths, routes, middlewares, response status code, parameters in the path, query and body. It is possible to add information such as: endpoint description, parameter description, defininitions, security, among others. It is also possible to ignore or disable the automatic capture of an endpoint (in the latter case, having to manually add each information). The module generates the *.json* file with the documentation in the swagger format.

[![NPM Version](http://img.shields.io/npm/v/swagger-autogen.svg?style=flat)](https://www.npmjs.org/package/commander)
[![NPM Downloads](https://img.shields.io/npm/dm/swagger-autogen.svg?style=flat)](https://npmcharts.com/compare/swagger-autogen?minimal=true)
[![Known Vulnerabilities](https://snyk.io/test/npm/swagger-autogen/badge.svg)](https://snyk.io/test/npm/swagger-autogen)


## Contents

- [Installation](#installation)
- [Update](#update)
- [Usage](#usage)
  - [Usage (Basic)](#usage-basic)
  - [Usage (With Optionals)](#usage-with-optionals)
- [Building documentation without starting the project](#building-documentation-without-starting-the-project)
- [Building documentation at project startup](#building-documentation-at-project-startup)
- [Options](#options)
- [Endpoints](#endpoints)
  - [Automatic capture](#automatic-capture)
  - [Tags](#tags)
  - [Summary](#summary)
  - [Description](#description)
  - [Operation ID](#operation-id)
  - [Consumes and Produces](#consumes-and-produces)
  - [Parameters](#parameters)
  - [Responses](#responses)
  - [Schema and Definitions](#schema-and-definitions)
    - [Examples of Definitions](#examples-of-definitions)
  - [Endpoint as deprecated](#endpoint-as-deprecated)
  - [Ignoring endpoint](#ignoring-endpoint)
  - [Manual capture](#manual-capture)
  - [Forced endpoint creation](#forced-endpoint-creation)
- [Security](#security)
  - [API Keys (Token) example](#api-keys-token-example)
  - [OAuth2 example](#oauth2-example)
- [Response Language](#response-language)
- [Examples](#examples)
- [Compatibility](#compatibility)
- [Tutorials](#tutorials)
- [Changelog](#changelog)
- [Help us!](#help-us)
- [License](#license)

## Installation

This is a [Node.js](https://nodejs.org/en/) module available through the [npm](https://www.npmjs.com/).

```bash
$ npm install --save-dev swagger-autogen
```

It is loaded using the require() function:

```js
const swaggerAutogen = require('swagger-autogen')()
```

## Update

If you already have the module installed and want to update to the latest version, use the command:

```bash
$ npm install --save-dev swagger-autogen@2.8.1
```

## Usage

[Example using Router](https://github.com/davibaltar/example-swagger-autogen-with-router)

[Example without Router](https://github.com/davibaltar/example-swagger-autogen)

[See Tutorial in English](https://medium.com/@davibaltarx/automatic-api-documentation-in-node-js-using-swagger-dd1ab3c78284) 

[See Tutorial em Português Brasil](https://medium.com/@davibaltarx/documenta%C3%A7%C3%A3o-autom%C3%A1tica-de-apis-em-node-js-eb03041c643b) 

The two sections below will show the most basic and most complete use of this module.

Function signature: 
```js
const swaggerAutogen: (outputFile: <string>, endpointsFiles: <Array of string>, data: <object>) => Promise <any>
```

**outputFile:** (Required*). Output file. It will be the file generated by the module containing the documentation in the format identified by Swagger.

**endpointsFiles:** (Required*). Files containing the endpoints. These are the files that contain methods such as get, post, put, and so on, for example: `app.get('/path', ...)` or `route.post('/path', ...)`.

**doc:** (Not Required). Object containing the details of the documentation. If not informed, or if any parameter of the object is omitted, the default values ​​will be used. (See: [Usage (With optionals)](#usage-with-optionals) section)

### Usage (Basic)
The code below must be inserted in a separate file, for example: *swagger.js*. For example:

**File: swagger.js** 
```js
const swaggerAutogen = require('swagger-autogen')()

const doc = {
    info: {
        title: "My API",
        description: "Description"
    },
    host: "localhost:3000",
    schemes: ['http']
}

const outputFile = './path/swagger-output.json'
const endpointsFiles = ['./path/endpointsUser.js', './path/endpointsBook.js']

/* NOTE: if you use the express Router, you must pass in the 
   'endpointsFiles' only the root file where the route starts,
   such as: index.js, app.js, routes.js, ... */

swaggerAutogen(outputFile, endpointsFiles, doc)
```

### Usage (With optionals)
The code below must be inserted in a separate file, for example:

**File: swagger.js** 
```js
const swaggerAutogen = require('swagger-autogen')()

const doc = {
    info: {
        "version": "",                // by default: "1.0.0"
        "title": "",                  // by default: "REST API"
        "description": ""             // by default: ""
    },
    host: "",                         // by default: "localhost:3000"
    basePath: "",                     // by default: "/"
    schemes: [],                      // by default: ['http']
    consumes: [],                     // by default: ['application/json']
    produces: [],                     // by default: ['application/json']
    tags: [                           // by default: empty Array
        {
            "name": "",               // Tag name
            "description": ""         // Tag description
        },
        // { ... }
    ],
    securityDefinitions: { },         // by default: empty object
    definitions: { }                  // by default: empty object
}

const outputFile = './path/swagger-output.json'
const endpointsFiles = ['./path/endpointsUser.js', './path/endpointsBook.js']

/* NOTE: if you use the express Router, you must pass in the 
   'endpointsFiles' only the root file where the route starts,
   such as: index.js, app.js, routes.js, ... */

swaggerAutogen(outputFile, endpointsFiles, doc)
```

**NOTE:** To omit any of the attributes in the *.json* file, just assign the value **null** to the specified attribute in the **doc**.  

## Building documentation without starting the project
To build the documentation without starting your project, add the following script to your project's *package.json* file:

**File: package.json** 
```js
    // { ... },
    "scripts": {
        // ... ,
        "swagger-autogen": "node ./swagger.js"
    }
```

Where `./swagger.js` is the file containing the `swaggerAutogen(...)` function call (see section [Usage](#usage). After that, at the root of your project, run the following command:

```bash
$ npm run swagger-autogen
``` 

## Building documentation at project startup
To build the documentation before the project starts and immediately start it, rewrite the `swaggerAutogen(...)` function as follows:

```js
// ...
swaggerAutogen(outputFile, endpointsFiles, doc).then( () => {
    require('./index.js')           // Your project's root file
})
```

Where *index.js* is your project's root file. Change the *start* script in your project's *package.json* to point to the file containing the `swaggerAutogen(...)` function. If you use Visual Studio Code, change the reference in your *launch.json* in the same way. Now, just run your project as usual. With that the documentation will be generated and soon after the project will start, automatically updating the documentation as soon as the project start.

See: [Complete example](https://github.com/davibaltar/example-swagger-autogen)

## Options
It is possible to change some options of the module by passing an object as a parameter. This object is **optional**.

```js
const options = {
    openapi: <string>,          // By default is null
    language: <string>,         // By default is 'en-US'
    disableLogs: <boolean>,     // By default is false
    disableWarnings: <boolean>  // By default is false
}

const swaggerAutogen = require('swagger-autogen')(options)
```

**OpenAPI:** To enable OpenAPI v3, assign a version, such as "3.0.0" to the *openapi* parameter. In the future, OpenAPI v3 will be the default. 

To see the available languages, go to the section [Response Language](#response-language)

## Endpoints
The way to configure the module is done within comments, and can be in the format `// ...` or `/* ... */`. The used pattern will be `#swagger.something` tag. Each comment can contain one or more `#swagger.something` tags. **NOTE:** ALL COMMENTS CONTAINING `#swagger.something` MUST BE WITHIN THE FUNCTIONS.

### Automatic capture
In this case it is not necessary to do anything. Considering, for example, if the pattern of your API is as follows:

```js
    ...
    app.post('/users', (req, res) => {
        ...
        users.addUser(req.query.obj)
        ...
        if(...)
            return res.status(201).send(data)
        ...
        return res.status(500).send(false)
    })
    ...
```

The recognition of the method, path, parameters and status of the response will be automatic.

See an [example here!](#examples)

### Tags
To inform which tags the endpoinst belongs to, use the `#swagger.tags` tag, for example:

```js
    ...
    app.get('/users', (req, res) => {
        ...
        // #swagger.tags = ['Users']
        ...
    })
```

### Summary
This is the summary of the Endpoint. To add it, use the `#swagger.summary` tag, for example:

```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        // #swagger.summary = 'Your summary here'
        ...
    })
```

### Description
This is the description of the Endpoint. To add it, use the `#swagger.description` tag, for example:

```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        // #swagger.description = 'Endpoint used to obtain a user.'
        ...
    })
```

### Operation ID
This is the operationId of the Endpoint. To add it, use the `#swagger.operationId` tag, for example:

```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        // #swagger.operationId = 'Your_operationId_here'
        ...
    })
```

### Consumes and Produces
Use the `#swagger.produces = ['contentType']` or `#swagger.consumes = ['contentType']` tag to add a new produce or a new consume, respectively. In the **Example (Consumes)** below, the two endpoints will have the same result in the documentation.

**Example (Consumes):**  
```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        // Recognizes the 'consumes' automatically
        res.setHeader('Content-Type', 'application/xml')
        ...
    })
```
OR
```js
    app.get('/users/:id', (req, res) => {
        ...
        // #swagger.consumes = ['application/xml']
        ...
    })
```

**Example (Produces):**  
```js
    ...
    app.get('/v2/users/:id', (req, res) => {
        ...
        // #swagger.produces = ['application/json']
        ...
    })
```

### Parameters
It is possible to create or complement automatically detected parameters. Use the `#swagger.parameters['parameterName']` tag to create a new parameter or to complete an existing parameter (automatically detected).

All optional parameters:
```js
/* #swagger.parameters['parameterName'] = {
        in: <string>,
        description: <string>,
        required: <boolean>,
        type: <string>,
        format: <string>,
        schema: <object> or <Array>
} */
```

**in:** 'path', 'query' or 'body'               // by default is 'query'  
**description:** The parameter description  
**required:** true or false  
**type:** 'string', 'integer', 'object', etc.   // by default is 'string'  when 'schema' is missing
**format:** 'int64', etc.  
**schema:** See section [Schema and Definitions](#schema-and-definitions)  

Some examples:
```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        //  #swagger.parameters['id'] = { description: "User ID" } 
        ...
    })

    app.post('/books', (req, res) => {
        ...
        /*  #swagger.parameters['obj'] = {
                in: 'body',
                type: "object",
                description: "Book data"
        } */
        users.addUser(req.body)
        ...
    })

    app.post('/users', (req, res) => {
        ...
        /*  #swagger.parameters['obj'] = { 
                in: 'body',
                description: "Add a user",
                schema: { $ref: "#/definitions/AddUser" }
        } */
        ...
    })

    app.get('/users', async (req, res) => {
        /*  #swagger.parameters['item'] = { 
                in: 'query',
                description: "Any item..."
        } */
        let test = req.query.item
    });

```

Click here to see: ["#/definitions/AddUser"](#schema-and-definitions)

#### Body  

The **body** is automatically recognized, for example:

```js
    app.post('/users', (req, res) => {

        const myItem1 = req.body.item1

        const { item2, item3 } = req.body

        ...
    })
```

**NOTE:** But, if there is any `#swagger.parameters[...] = { in: 'body', ... }` with **schema** declared, the recognition of **body** will be ignored, for example:

```js
    app.post('/users', (req, res) => {
        /*  #swagger.parameters['parameter_name'] = {
                in: 'body',
                description: "Any description...",
                schema: {
                    $name: "Jhon Doe",
                    $age: 29,
                    about: ""
                }
        } */

        const myItem1 = req.body.item1  // Will be ignored

        const { item2, item3 } = req.body  // Will be ignored

        ...
    })
```

However, if you wish to add more information to the automatically recognized **body**, declared the `#swagger.parameters` adding **in: 'body'**, BUT without the **schema**, such as:

```js
    app.post('/users', (req, res) => {
        /*  #swagger.parameters['any_name'] = {
               in: 'body',
               description: "Any description..."
        } */

        const myItem1 = req.body.item1

        const { item2, item3 } = req.body

        ...
    })
```

Automatically the **body** will be recognized and the parameters 'any_name' and 'description' will be assigned to the **body**.

### Responses
It is possible to create or complement automatically detected responses. Use the `#swagger.reponses[statusCode]` tag to create a new answer or to complete an existing answer (automatically detected).

All optional parameters:
```js
/* #swagger.responses[<number>] = {
        description: <string>,
        schema: <object> or <Array>
} */
```

**description:** The parameter description.  
**schema:** See section [Schema and Definitions](#schema-and-definitions)  

For example:

```js
    ...
    app.get('/users/:id', (req, res) => {

        if(...)
            return res.status(404)

        try {
            
            /* #swagger.responses[200] = { 
                    description: 'User successfully obtained.',
                    schema: { $ref: "#/definitions/User" } 
            } */
            return res.status(200).send(data)

        } catch (err) {
            // #swagger.responses[500] = { description: 'Problem with the server.' }
            return res.status(500)
        }

    })
```

**NOTE:** For more information about **schema** and **definitions**, see the section: [Schema and Definitions](#schema-and-definitions)  

**NOTE:** As the 404 status description was not entered, "Not Found" will automatically be added. It is possible to change the language of the automatic response, see the [Response Language](#response-language) section.  

### Schema and Definitions
Unlike how Swagger writes, the answers in this module are added in a simpler way, that is, in the way you want to see the result. These responses can be added to the *definitions* parameter of the *doc* object seen in the [Usage](#usage) section, or directly to the response via the *schema* parameter.

**About Examples and Types in schema:** The example comes right in front of the parameter declaration, and the type is abstracted according to the *typeof* of the example. In the code below, the parameter "name" will have as an example "Jhon Doe" and type string, while "age" will have as an example 29 and type number.

**NOTE:** To configure a parameter as **required**, just add the symbol **$** before the parameter, for example: `$name = "Jhon Doe"`.

For example:

```js
const doc = {
    // { ... },
    definitions: {
        Parents: {
            father: "Simon Doe",
            mother: "Marie Doe"
        },
        User: {
            name: "Jhon Doe",
            age: 29,
            parents: {
                $ref: '#/definitions/Parents'
            },
            diplomas: [
                {
                    school: "XYZ University",
                    year: 2020,
                    completed: true,
                    internship: {
                        hours: 290,
                        location: "XYZ Company"
                    }
                }
            ]
        },
        AddUser: {
            $name: "Jhon Doe",
            $age: 29,
            about: ""
        },
       // { ... }
    }
}
```

`Endpoint file:`
```js
    app.post('/users', (req, res) => {
        ...
        /*    #swagger.parameters['obj'] = { 
                in: 'body',
                description: "Adding new user.",
                schema: { $ref: "#/definitions/AddUser" }
        } */
        ...
    })
```

or inserting directly, without using definitions:
```js
    app.post('/users', (req, res) => {
        ...
        /*    #swagger.parameters['obj'] = { 
                in: 'body',
                description: "Adding new user.",
                schema: {
                    $name: "Jhon Doe",
                    $age: 29,
                    about: ""
                }
        } */
        ...
    })
```

#### Examples of Definitions
The following are some examples of definitions:

**Definitions:** 
```js
const doc = {
    // { ... },
    definitions: {
        myBoolean: true,
        myNumber: 123,
        myString: "my example",
        myObject: {
            field: "my example"
        },
        myArrayOfBooleans: [ 
            true 
        ],
        myArrayOfNumbers: [ 
            123 
        ],
        myArrayOfStrings: [ 
            "my example" 
        ],
        myArrayOfObjects: [ 
            {
                field: "my example"
            }
        ],
        myReferencedObjectArray: [
            { $ref: "#/definitions/myObject" }
        ]
    }
}
```

**Endpoint:** 
```js
app.get('/responses', (req, res) => {
  /* #swagger.responses[001] = {
      description: "myBoolean",
      schema: { $ref: "#/definitions/myBoolean" }
  } */

  /* #swagger.responses[002] = {
      description: "myNumber",
      schema: { $ref: "#/definitions/myNumber" }
  } */

  /* #swagger.responses[003] = {
      description: "myString",
      schema: { $ref: "#/definitions/myString" }
  } */

  /* #swagger.responses[004] = {
      description: "myObject",
      schema: { $ref: "#/definitions/myObject" }
  } */

  /* #swagger.responses[005] = {
      description: "myArrayOfBooleans",
      schema: { $ref: "#/definitions/myArrayOfBooleans" }
  } */

  /* #swagger.responses[006] = {
      description: "myArrayOfNumbers",
      schema: { $ref: "#/definitions/myArrayOfNumbers" }
  } */

  /* #swagger.responses[007] = {
      description: "myArrayOfStrings",
      schema: { $ref: "#/definitions/myArrayOfStrings" }
  } */

  /* #swagger.responses[008] = {
      description: "myArrayOfObjects",
      schema: { $ref: "#/definitions/myArrayOfObjects" }
  } */

  /* #swagger.responses[009] = {
      description: "myReferencedObjectArray",
      schema: { $ref: "#/definitions/myReferencedObjectArray" }
  } */
})
```

The result will be:

![](https://raw.githubusercontent.com/davibaltar/public-store/master/example-of-definitions.png)

### Endpoint as deprecated
Use the `#swagger.deprecated = true` tag to inform that a given endpoint is depreciated, for example:

```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        // #swagger.deprecated = true
        ...
    })
```

### Ignoring endpoint
Use the `#swagger.ignore = true` tag to ignore a given endpoint. Thus, it will not appear in the documentation, for example:

```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        // #swagger.ignore = true
        ...
    })
```

### Manual capture
Use the `#swagger.auto = false` tag to disable automatic recognition. With that, all parameters of the endpoint must be informed manually, for example:

```js
    ...
    app.put('/users/:id', (req, res) => {
    ...
        /*  #swagger.auto = false

            #swagger.path = '/users/{id}'
            #swagger.method = 'put'
            #swagger.produces = ["application/json"]
            #swagger.consumes = ["application/json"]

            #swagger.parameters['id'] = {
                in: 'path',
                description: 'User ID.',
                required: true,
                type: 'integer'
            }

            #swagger.parameters['obj'] = {
                in: 'body',
                description: 'User data.',
                required: true,
                type: 'string'
            }
        */
        ...
        if(...) {
            // #swagger.responses[201] = { description: "User registered successfully." }
            return res.status(201).send(data)
        }
        ...
        // #swagger.responses[500] = { description: "Server failure."}
        return res.status(500).send(false)
    })
```

### Forced Endpoint Creation
If you want to forcibly create an endpoint, use the  `#swagger.start` and` #swagger.end` tags, for example:

```js
function myFunction(param) {
    // #swagger.start
    ...
    /*
        #swagger.path = '/forcedEndpoint/{id}'
        #swagger.method = 'put'
        #swagger.description = 'Forced endpoint.'
        #swagger.produces = ["application/json"]
    */
    ...
    /*  #swagger.parameters['id'] = {
            in: 'path',
            type: 'integer',
            description: 'User ID.' } */
    const dataId = users.getUser(req.params.id)
    ...
    /*  #swagger.parameters['obj'] = {
            in: 'query',
            description: 'User data.',
            schema: { $ref: "#/definitions/AddUser" }
    } */
    const dataObj = users.getUser(req.query.obj)
    ...
    if (...)
        return res.status(200).send(true)    // #swagger.responses[200]
    ...
    return res.status(404).send(false)       // #swagger.responses[404]
    ...
    // #swagger.end
}
```

## Security
It is possible to add security to endpoints. The following are some examples, but a complete approach can be seen on the website [swagger.io](#https://swagger.io/docs/specification/authentication)

### API Keys (Token) example
The security example below was taken from the original Swagger documentation.

```js
const doc = {
    // { ... },
    securityDefinitions:{
        apiKeyAuth:{
            type: "apiKey",
            in: "header",       // can be "header", "query" or "cookie"
            name: "X-API-KEY",  // name of the header, query parameter or cookie
            description: "any description..."
        }
    }
}
```

To see more about the properties of the **doc**, see the [Usage (With Optionals)](#usage-with-optionals) section.

At the endpoint, add the `#swagger.security` tag, for example:
```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        /* #swagger.security = [{
               "apiKeyAuth": []
        }] */
        ...
    })
```

### OAuth2 example
The security example below was taken from the original Swagger documentation.

```js
const doc = {
    // { ... },
    securityDefinitions: {
        oAuthSample: {
            type: "oauth2",
            authorizationUrl: "https://petstore.swagger.io/oauth/authorize",
            flow: "implicit",
            scopes: {
                read_pets: "read your pets",
                write_pets: "modify pets in your account"
            }
        }
    }
}
```

To see more about the properties of the **doc**, see the [Usage (With Optionals)](#usage-with-optionals) section.

At the endpoint, add the `#swagger.security` tag, for example:
```js
    ...
    app.get('/users/:id', (req, res) => {
        ...
        /* #swagger.security = [{
            "oAuthSample": [
                "write_pets",
                "read_pets"
            ]
        }] */
        ...
    })
```

## Response Language
It is possible to change the default language (English) of the description in the automatic response, for example: status code 404, the description will be: 'Not Found'. To change, pass an object with the following parameter:


**Portuguese (Brazil)**  
```js
const swaggerAutogen = require('swagger-autogen')({ language: 'pt-BR' }) 
// In this case, for example, the description of status code 404 will be: 
// 'Não Encontrado'
```

OR

**Chinese (Simplified)**  
```js
const swaggerAutogen = require('swagger-autogen')({ language: 'zh-CN' }) 
// In this case, for example, the description of status code 404 will be: 
// '未找到'
```

OR 

**English (by default)**  
```js
const swaggerAutogen = require('swagger-autogen')()
// In this case, for example, the description of status code 404 will be: 
// 'Not Found'
```


For now, the module has only the languages: English, Portuguese (Brazil) and Chinese (Simplified).

## Examples
Links to projects that covers the simplest use of this module as well as the most complete use. See the links below:

[Example using Router](https://github.com/davibaltar/example-swagger-autogen-with-router)

[Example without Router](https://github.com/davibaltar/example-swagger-autogen)

See the result after construction in the image below:

![](https://raw.githubusercontent.com/davibaltar/public-store/master/screen-swagger-autogen.png)


## Compatibility
This module is independent of any framework. For the recognition to be **automatic**, it is necessary that your framework follow the pattern **foo.method(path, callback)**, where *foo* is the variable belonging to the server or the route, such as: *app*, *server*, *route*, etc. The *method* are HTTP methods, such as get, post, put, and so on. If the **foo.method(path, callback)** pattern is not found in the files, it will be necessary to **manually** enter the beginning and end of the endpoint using the `#swagger.start` and `#swagger.end` tags (see the section: [Forced Endpoint Creation](#forced-endpoint-creation)). If you use the *Express.js* framework, the status code and produces will be automaticaly obtained according to the *status()* and *setHeader()* functions, respectively. If you use a framework that does not contain these functions, you will need to manually add them with the `#swagger.response[statusCode]` and `#swagger.produces` tags (see the [Responses](#responses) and [Consumes and Produces](#consumes-and-produces) sections).

**Swagger version:** 2.0  

## Tutorials
Some tutorials with examples:

[Tutorial in English](https://medium.com/@davibaltarx/automatic-api-documentation-in-node-js-using-swagger-dd1ab3c78284) 

[Tutorial em Português Brasil](https://medium.com/@davibaltarx/documenta%C3%A7%C3%A3o-autom%C3%A1tica-de-apis-em-node-js-eb03041c643b) 


## Changelog
- Version 2.0.x:
  - Recognizes of Routes and referenced functions
  - Endpoint with referenced callback now it's done automatically
  - Multiple patterns now it's done automatically
  - Partial TypeScript recognition
  - Recognizes middleware and middleware array
  - Code refactoring
  - Bug fix
- Version 2.1.x:
  - Recognizes different file import patterns
  - Recognizes some more features of TypeScript
  - Bug fix
- Version 2.2.x:
  - Recognizes some more features of TypeScript
  - Performance improvement
  - Recognizes regex in endpoint's path
  - Recognizes middlewares of routes (partially)
  - Options to disable logs
  - Bug fix
- Version 2.3.x:
  - Recognizes 'require-dir' lib (partially)
  - Recognizes some more features of TypeScript
  - Bug fix
- Version 2.4.x:
  - Recognizes direct import, such as: router.use(..., require('./routes.js'))
  - Recognizes new Router({ prefix: '...' })
  - Added some default parameters values
  - Code refactoring
  - Bug fix
- Version 2.5.x:
  - New features: #swagger.summary and #swagger.operationId
  - Bug fix
- Version 2.6.x:
  - Recognition of more patterns
  - Bug fix
- Version 2.7.x:
  - Automatic body recognition
  - Automatic 'destructuring' recognition (query and body)
  - Bug fix
- Version 2.8.x:
  - OpenAPI option
  - Bug fix


**TODO:**  
  - Recognize more TypeScript's features 
  - Recognize middlewares of routes (completely)
  - Recognize regex in middlewares
  - Recognize multiples "express.Router()" in the same file"
  - Recognize referenced path, such as: foo.method(\`/${fooPath1}\` + '/' + fooPath2, foo...)
  - Recognize 'alias' in the import files
  - Write more test cases
  - Improve performance
  - Refactor code
  - Integrate with other frameworks

## Help us!
Help us improve this module. If you have any information that the module does not provide or provides incompletely or incorrectly, please use our [Github](https://github.com/davibaltar/swagger-autogen) repository.

**pt-BR:**
Ajude-nos a melhorar este módulo. Se você tiver alguma informação que o módulo não forneça ou forneça de maneira incompleta ou incorreta, use o nosso repositório do [Github](https://github.com/davibaltar/swagger-autogen). Pode enviar em português Brasil também! :)

Repository: https://github.com/davibaltar/swagger-autogen

## License
  [MIT](LICENSE) License
