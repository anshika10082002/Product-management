#Lithium
## Project - Products Management

- This backend project provides APIs for product management, including user registration, login, product, cart, and order management. It uses Node.js and MongoDB with Mongoose liberary to connect to the database, and AWS S3 and JWT for authentication and authorization in some APIs.

- In this project we are changing how we send token with a request. Instead of using a custom header key like x-api-key, you need to use Authorization header and send the JWT token as Bearer token.Using bcrypt liberary to bcrypt password.

- To run the project locally, you'll need to have Node.js and MongoDB installed. Then, follow these steps:

1.Clone the repository
2.Install dependencies by running  *npm install*



## FEATURE I - User
### Models
- User Model
```yaml
{ 
  fname: {string, mandatory},
  lname: {string, mandatory},
  email: {string, mandatory, valid email, unique},
  profileImage: {string, mandatory}, // s3 link
  phone: {string, mandatory, unique, valid Indian mobile number}, 
  password: {string, mandatory, minLen 8, maxLen 15}, // encrypted password
  address: {
    shipping: {
      street: {string, mandatory},
      city: {string, mandatory},
      pincode: {number, mandatory}
    },
    billing: {
      street: {string, mandatory},
      city: {string, mandatory},
      pincode: {number, mandatory}
    }
  },
  createdAt: {timestamp},
  updatedAt: {timestamp}
}
```


## User APIs 
### POST /register
- Endpoint: /register
- Method: POST
- Parameters:
    - name: User's name (required)
    - email: User's email (required)
    - password: User's password (required)

- Uploading image to S3 bucket and saving it's public url in user document.
- Saving password in encrypted format
- __Response format__
  - _**On success**_ - Returning HTTP status 201 with user document. 
  - _**On error**_ - Returning a suitable error message with a valid HTTP status code. 
```yaml
{
    "status": true,
    "message": "User created successfully",
    "data": {
        "fname": "John",
        "lname": "Doe",
        "email": "johndoe@mailinator.com",
        "profileImage": "https://classroom-training-bucket.s3.ap-south-1.amazonaws.com/user/copernico-p_kICQCOM4s-unsplash.jpg",
        "phone": 9876543210,
        "password": "$2b$10$DpOSGb0B7cT0f6L95RnpWO2P/AtEoE6OF9diIiAEP7QrTMaV29Kmm",
        "address": {
            "shipping": {
                "street": "MG Road",
                "city": "Indore",
                "pincode": 452001
            },
            "billing": {
                "street": "MG Road",
                "city": "Indore",
                "pincode": 452001
            }
        },
        "_id": "6162876abdcb70afeeaf9cf5",
        "createdAt": "2021-10-10T06:25:46.051Z",
        "updatedAt": "2021-10-10T06:25:46.051Z",
        "__v": 0
    }
}
```

### POST /login
- Endpoint: /login
- Method: POST
- Parameters:
    - email: User's email (required)
    - password: User's password (required)

- __Response format__
  - _**On success**_ - Returning HTTP status 200 and JWT token and UserId in response body
  - _**On error**_ - Return a suitable error message with a valid HTTP status code. 
```yaml
{
    "status": true,
    "message": "User login successfull",
    "data": {
        "userId": "6165f29cfe83625cf2c10a5c",
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI2MTYyODc2YWJkY2I3MGFmZWVhZjljZjUiLCJpYXQiOjE2MzM4NDczNzYsImV4cCI6MTYzMzg4MzM3Nn0.PgcBPLLg4J01Hyin-zR6BCk7JHBY-RpuWMG_oIK7aV8"
    }
}
```

## GET /user/:userId/profile (Authentication required)
- Endpoint: /user/:userId/profile
- Method: GET

- userId in url param and in token is same
- __Response format__
  - _**On success**_ - Returning HTTP status 200 with user document. 
  - _**On error**_ - Returning a suitable error message with a valid HTTP status code. 
```yaml
{
    "status": true,
    "message": "User profile details",
    "data": {
        "address": {
            "shipping": {
                "street": "MG Road",
                "city": "Indore",
                "pincode": 452001
            },
            "billing": {
                "street": "MG Road",
                "city": "Indore",
                "pincode": 452001
            }
        },
        "_id": "6162876abdcb70afeeaf9cf5",
        "fname": "John",
        "lname": "Doe",
        "email": "johndoe@mailinator.com",
        "profileImage": "https://classroom-training-bucket.s3.ap-south-1.amazonaws.com/user/copernico-p_kICQCOM4s-unsplash.jpg",
        "phone": 9876543210,
        "password": "$2b$10$DpOSGb0B7cT0f6L95RnpWO2P/AtEoE6OF9diIiAEP7QrTMaV29Kmm",
        "createdAt": "2021-10-10T06:25:46.051Z",
        "updatedAt": "2021-10-10T06:25:46.051Z",
        "__v": 0
    }
}
```

## PUT /user/:userId/profile (Authentication and Authorization required)
- Endpoint: /user/:userId/profile
- Method: PUT

- Allowing an user to update their profile.
- A user can update all the fields
-  userId in url param and in token is same

- __Response format__
  - _**On success**_ - Returning HTTP status 200 with updated user document. 
  - _**On error**_ - Return a suitable error message with a valid HTTP status code. 
```yaml
{
    "status": true,
    "message": "User profile updated",
    "data": {
        "address": {
            "shipping": {
                "street": "MG Road",
                "city": "Delhi",
                "pincode": 110001
            },
            "billing": {
                "street": "MG Road",
                "city": "Indore",
                "pincode": 452010
            }
        },
        "_id": "6162876abdcb70afeeaf9cf5",
        "fname": "Jane",
        "lname": "Austin",
        "email": "janedoe@mailinator.com",
        "profileImage": "https://classroom-training-bucket.s3.ap-south-1.amazonaws.com/user/laura-davidson-QBAH4IldaZY-unsplash.jpg",
        "phone": 9876543210,
        "password": "$2b$10$jgF/j/clYBq.3uly6Tijce4GEGJn9EIXEcw9NI3prgKwJ/6.sWT6O",
        "createdAt": "2021-10-10T06:25:46.051Z",
        "updatedAt": "2021-10-10T08:47:15.297Z",
        "__v": 0
    }
}
```

Note: [Bcrypt](https://www.npmjs.com/package/bcrypt)
Send [form-data](https://developer.mozilla.org/en-US/docs/Web/API/FormData)

## FEATTURE II - Product
### Models
- Product Model
```yaml
{ 
  title: {string, mandatory, unique},
  description: {string, mandatory},
  price: {number, mandatory, valid number/decimal},
  currencyId: {string, mandatory, INR},
  currencyFormat: {string, mandatory, Rupee symbol},
  isFreeShipping: {boolean, default: false},
  productImage: {string, mandatory},  // s3 link
  style: {string},
  availableSizes: {array of string, at least one size, enum["S", "XS","M","X", "L","XXL", "XL"]},
  installments: {number},
  deletedAt: {Date, when the document is deleted}, 
  isDeleted: {boolean, default: false},
  createdAt: {timestamp},
  updatedAt: {timestamp},
}
```


### POST /products (_No authentication required_)
- Endpoint: /products
- Method: POST

- Uploading product image to S3 bucket and saving image public url in document.
- __Response format__
  - _**On success**_ - Returning HTTP status 201 with product document.
  - _**On error**_ - Returning a suitable error message with a valid HTTP status code. 

### GET /products
 - Endpoint:/products
 - Method: GET
- Returning all products in the collection that aren't deleted.
  - __Filters__
    - Size ( key - 'size')
    - Product name ( key - 'name'). Returning all products with pattern matching name
    - Price : greater than or less than a specific value.( keys - 'priceGreaterThan' and 'priceLessThan'.) 
    
> **_NOTE:_** For price filter request can contain both or any one of the keys. 
  example- { priceGreaterThan: 500, priceLessThan: 2000 } or just { priceLessThan: 1000 } )
    
  - __Sort__
    - Sorted by product price in ascending or descending. 
    - The key value pair will look like {priceSort : 1} or {priceSort : -1}
  _eg_ /products?size=XL&name=Nit%20grit
- __Response format__
  - _**On success**_ - Returning HTTP status 200 with product documents.  [this]
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.  [this]

### GET /products/:productId
- Endpoint:/products/:productId
 - Method: GET
- Returns product details by product id
- __Response format__
  - _**On success**_ - Return HTTP status 200 with product documents.  [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.  [this]

### PUT /products/:productId
 - Endpoint:/products/:productId
 - Method: PUT
- Updates a product by changing at least one or all fields
- Check if the productId exists (must have isDeleted false and is present in collection). 
- If it doesn't, return an HTTP status 404 with a response body like [this](#error-response-structure)
- __Response format__
  - _**On success**_ - Return HTTP status 200 with updated product document.  [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.[this](#error-response-structure)

### DELETE /products/:productId
 - Endpoint:/products/:productId
 - Method: DELETE

- Deletes a product by product id (not deleted)
- __Response format__
  - _**On success**_ - Return HTTP status 200 [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.  [this](#error-response-structure)



## FEATURE III - Cart
### Models
- Cart Model
```yaml
{
  userId: {ObjectId, refs to User, mandatory, unique},
  items: [{
    productId: {ObjectId, refs to Product model, mandatory},
    quantity: {number, mandatory, min 1}
  }],
  totalPrice: {number, mandatory, comment: "Holds total price of all the items in the cart"},
  totalItems: {number, mandatory, comment: "Holds total number of items in the cart"},
  createdAt: {timestamp},
  updatedAt: {timestamp},
}
```

 
### POST /users/:userId/cart (Add to cart)(_authentication required as authorization header - bearer token_)
- Endpoint:/users/:userId/cart
 - Method: POST

- Create a cart for the user if it does not exist. Else add product(s) in cart.
- Get cart id in request body.
- Get productId in request body.
- Make sure that cart exist.
- Add a product(s) for a user in the cart.
- Make sure the userId in params and in JWT token match.
- Make sure the user exist
- Make sure the product(s) are valid and not deleted.
- Get product(s) details in response body.
- __Response format__
  - _**On success**_ - Returning HTTP status 201 with cart document. [this](#successful-response-structure)
  - _**On error**_ - Returning a suitable error message with a valid HTTP status code.[this](#error-response-structure)

### PUT /users/:userId/cart 
- Endpoint:/users/:userId/cart
- Method: PUT
- _**In request body**_
  - cartId
  - productId

- Updates a cart by either decrementing the quantity of a product by 1 or deleting a product from the cart.
- Get key 'removeProduct' in request body. 
- Make sure that cart exist.
- Key 'removeProduct' denotes whether a product is to be removed({removeProduct: 0}) or its quantity has to be decremented by 1({removeProduct: 1}).
- Make sure the userId in params and in JWT token match.
- Make sure the user exist
- Check if the productId exists and is not deleted before updating the cart.
- __Response format__
  - _**On success**_ - Return HTTP status 200 with updated cart document and product details.  [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.  [this](#error-response-structure)

### GET /users/:userId/cart
- Endpoint:/users/:userId/cart
- Method: GET

- Make sure that cart exist.
- Make sure the userId in params and in JWT token match.
- Make sure the user exist
- Get product(s) details in response body.
- __Response format__
  - _**On success**_ - Return HTTP status 200 with  cart document and product details.[this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code. [this](#error-response-structure)

### DELETE /users/:userId/cart

- Endpoint:/users/:userId/cart
- Method: DELETE

- Make sure that cart exist.
- Make sure the userId in params and in JWT token match.
- Make sure the user exist
- cart deleting means array of items is empty, totalItems is 0, totalPrice is 0.
- __Response format__
  - _**On success**_ - Return HTTP status 204. Return a suitable message.  [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.  [this](#error-response-structure)



## FEATURE IV - Order
### Models
- Order Model
```yaml
{
  userId: {ObjectId, refs to User, mandatory},
  items: [{
    productId: {ObjectId, refs to Product model, mandatory},
    quantity: {number, mandatory, min 1}
  }],
  totalPrice: {number, mandatory, comment: "Holds total price of all the items in the cart"},
  totalItems: {number, mandatory, comment: "Holds total number of items in the cart"},
  totalQuantity: {number, mandatory, comment: "Holds total number of quantity in the cart"},
  cancellable: {boolean, default: true},
  status: {string, default: 'pending', enum[pending, completed, cancled]},
  deletedAt: {Date, when the document is deleted}, 
  isDeleted: {boolean, default: false},
  createdAt: {timestamp},
  updatedAt: {timestamp},
}
```


 
### POST /users/:userId/orders (Authentication and authorization required)

- Endpoint:/users/:userId/orders
- Method: POST

- Make sure the userId in params and in JWT token match.
- Make sure the user exist
- Get cart details in the request body
- __Response format__
  - _**On success**_ - Return HTTP status 200 and order document.  [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code.  [this](#error-response-structure)

## PUT /users/:userId/orders

- Endpoint:/users/:userId/orders
- Method: POST

- Updates an order status
- Make sure the userId in params and in JWT token match.
- Make sure the user exist
- Get order id in request body
- Make sure the order belongs to the user
- Make sure that only a cancellable order could be canceled. Else send an appropriate error message and response.
- __Response format__
  - _**On success**_ - Return HTTP status 200. Also return the updated order document.  [this](#successful-response-structure)
  - _**On error**_ - Return a suitable error message with a valid HTTP status code. The response should be a JSON object like [this](#error-response-structure)

## Testing 
- To test these apis create a new collection in Postman named Project 5 Shopping Cart
- Each api should have a new request in this collection


## Response

### Successful Response structure
```yaml
{
  status: true,
  message: 'Success',
  data: {

  }
}
```
### Error Response structure
```yaml
{
  status: false,
  message: ""
}
```

## Collections
## users
```yaml
{
  _id: ObjectId("88abc190ef0288abc190ef02"),
  fname: 'John',
  lname: 'Doe',
  email: 'johndoe@mailinator.com',
  profileImage: 'http://function-up-test.s3.amazonaws.com/users/user/johndoe.jpg', // s3 link
  phone: 9876543210,
  password: '$2b$10$O.hrbBPCioVm237nAHYQ5OZy6k15TOoQSFhTT.recHBfQpZhM55Ty', // encrypted password
  address: {
    shipping: {
      street: "110, Ridhi Sidhi Tower",
      city: "Jaipur",
      pincode: 400001
    }, {mandatory}
    billing: {
      street: "110, Ridhi Sidhi Tower",
      city: "Jaipur",
      pincode: 400001
    }
  },
  createdAt: "2021-09-17T04:25:07.803Z",
  updatedAt: "2021-09-17T04:25:07.803Z",
}
```
### products
```yaml
{
  _id: ObjectId("88abc190ef0288abc190ef55"),
  title: 'Nit Grit',
  description: 'Dummy description',
  price: 23.0,
  currencyId: 'INR',
  currencyFormat: '₹',
  isFreeShipping: false,
  productImage: 'http://function-up-test.s3.amazonaws.com/products/product/nitgrit.jpg',  // s3 link
  style: 'Colloar',
  availableSizes: ["S", "XS","M","X", "L","XXL", "XL"],
  installments: 5,
  deletedAt: null, 
  isDeleted: false,
  createdAt: "2021-09-17T04:25:07.803Z",
  updatedAt: "2021-09-17T04:25:07.803Z",
}
```

### carts
```yaml
{
  "_id": ObjectId("88abc190ef0288abc190ef88"),
  userId: ObjectId("88abc190ef0288abc190ef02"),
  items: [{
    productId: ObjectId("88abc190ef0288abc190ef55"),
    quantity: 2
  }, {
    productId: ObjectId("88abc190ef0288abc190ef60"),
    quantity: 1
  }],
  totalPrice: 50.99,
  totalItems: 2,
  createdAt: "2021-09-17T04:25:07.803Z",
  updatedAt: "2021-09-17T04:25:07.803Z",
}
```

### orders
```yaml
{
  "_id": ObjectId("88abc190ef0288abc190ef88"),
  userId: ObjectId("88abc190ef0288abc190ef02"),
  items: [{
    productId: ObjectId("88abc190ef0288abc190ef55"),
    quantity: 2
  }, {
    productId: ObjectId("88abc190ef0288abc190ef60"),
    quantity: 1
  }],
  totalPrice: 50.99,
  totalItems: 2,
  totalQuantity: 3,
  cancellable: true,
  status: 'pending',
  createdAt: "2021-09-17T04:25:07.803Z",
  updatedAt: "2021-09-17T04:25:07.803Z",
}
```
