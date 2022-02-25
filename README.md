# Node Elasticsearch Tasks

## Task - 1

1. Add Employee API - API to add a single Employee to the application . 
Employee will have the following details
    - Employee name (get from input of the API)
    - E-mail (get from input of the API)
    - Mobile number (get from input of the API)
    - Joining Date (get from input of the API)
    - Role (get from input of the API)
Store the Employee into Elasticsearch

### Implementation

### Create Employee Details API

- Elasticsearch API to add a single Employee to the application.

```bash
PUT /employee/_doc/1
{
    "employee_name": "Ben",
    "email": "ben@xyz.com",
    "mobile_number": 798218222,
    "join_date": "2020-09-11",
    "role": "Manager"
}
```


**handlers/employeeHandler.js**

```js
const elasticClient = require('../utils/hostEsClient');
const asyncHandler = require('../middlewares/async');
const ErrorResponse = require('../utils/errorResponse');

exports.addEmployees = asyncHandler(async(req, res, next) => {
    const employees = await elasticClient.index({
        index: 'employee-api',
        body: req.body
    })
    if (!employees) {
        return next(new ErrorResponse('Error adding employees', 500));
    } else {
        return res.status(201).json({
            success: true,
            data: employees
        });
    }
});
```

**routes/routes.js**

```js
router.post('/employees', bodyParser, addEmployees);
```

**Postman API Request**

```url 
POST http://localhost:5000/api/v1/employee
```

### Get the Employee Details API

- Elasticsearch API to get the Employee details.

```bash
GET /employee-api/_search
{
  "size": 50, 
  "query": {
    "match_all": {}
  }
}
```

**handers/employeeHandler.js**

```js
const elasticClient = require('../utils/hostEsClient');
const asyncHandler = require('../middlewares/async');
const ErrorResponse = require('../utils/errorResponse');

exports.getEmployees =  asyncHandler(async(req, res, next) => {
    let query = {
        index: 'employee-api',
        body: { 
            size: 50,
            query: {match_all:{}}
        }
      } 
    const employees = await elasticClient.search(query);
    if (!employees) { 
        return next(new ErrorResponse('Employees not found', 404));
    } else {
        res.status(200).json({
            success: true,
            data: employees
        })
    }
  });
```

## Task - 2

2. List Employee API - API to List employee with pagination
API should have atleast following input
- from (basically page number)
- offset (page size)
- sortby (field name to sort the table)

### Implementation

### GET Employee Details API

- Elasticsearch API to get the Employee details.

```bash
GET /employee-api/_search
{
  "from": 1,
  "size": 5,
  "sort": [
    {
      "join_date": {
        "order": "desc"
      }
    }
  ]
}
```

**handlers/employeeHandler.js**

```js
const elasticClient = require('../utils/hostEsClient');
const asyncHandler = require('../middlewares/async');
const ErrorResponse = require('../utils/errorResponse');

exports.addEmployees = asyncHandler(async(req, res, next) => {
    const employees = await elasticClient.index({
        index: 'employee-api',
        body: req.body
    })
    if (!employees) {
        return next(new ErrorResponse('Error adding employees', 500));
    } else {
        return res.status(201).json({
            success: true,
            data: employees
        });
    }
});
```

**routes/routes.js**

```js
router.post('/employees', bodyParser, addEmployees);
```

**Postman API Request**

```url 
POST http://localhost:5000/api/v1/employee
```
