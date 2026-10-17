## What is a API
API (Application Programming Interface) is a way for two software applications to communicate with each other and exchange data or request actions.
![[Pasted image 20261010100609.png]]

## What is an API End Point
An endpoint is a specific URL where you can access a particular API operation.
GET https://api.example.com/weather

## What are HTTP Methods
An **HTTP method** (also known as an HTTP verb) is ==a command included in a web request that **tells a server what action to perform on a specific resource**==

| HTTP Method  | Core Purpose                                                                   | CRUD Equivalent | Real-World Example                                                     |
| ------------ | ------------------------------------------------------------------------------ | --------------- | ---------------------------------------------------------------------- |
| **`GET`**    | **Retrieve** data from the server. It should only read data and not modify it. | Read            | Loading a blog post or viewing a product list.                         |
| **`POST`**   | **Submit** new data to the server to create a resource.                        | Create          | Submitting a signup form or publishing a new comment.                  |
| **`PUT`**    | **Replace** an entire existing resource with updated data.                     | Update          | Updating your entire profile information profile page.                 |
| **`PATCH`**  | **Partially update** a resource, modifying only specific fields.               | Update          | Changing just your password without altering the rest of your profile. |
| **`DELETE`** | **Remove** a specific resource permanently from the server.                    | Delete          | Deleting a user account or removing a photo.                           |
## What is HTTPS then?

- HTTP — Hypertext Transfer Protocol. It defines how a client and server communicate.
    
- HTTPS — Hypertext Transfer Protocol Secure. It uses HTTP with TLS encryption to protect communication between the client and server.

HTTP — Not encrypted

You send:

`username: nimesh` `password: abc123`

The data is not encrypted by HTTP itself, so someone who can intercept the traffic may read it.

HTTPS — Encrypted

Your data is encrypted during transmission.

`8fA$2kL... (illustrative)`

An interceptor cannot normally read the original credentials from the encrypted traffic

## What is Pydantic in Python

Pydantic is a Python library used to **validate data, enforce data structures, and convert compatible input values into expected types**.

### Key Concepts

- `BaseModel`: Base class used to create Pydantic models.
    
- **Type annotations:** Define the expected data types of fields.
    
- **Validation:** Checks whether input data matches the model's requirements.
    
- **Type conversion:** Converts compatible input values when possible.
    

### Example

```
from pydantic import BaseModel

class Product(BaseModel):
    id: int
    name: str
    price: float
    quantity: int

product = Product(
    id="1",
    name="Laptop",
    price="999.99",
    quantity="5"
)

print(product.id)    # 1
print(product.price) # 999.99
```

Pydantic validates the data and converts compatible string values into the expected numeric types.

### Pydantic with FastAPI

```
@app.post("/products")
def create_product(product: Product):
    return product
```

- FastAPI uses Pydantic to validate incoming request data.
    
- Valid data is passed to the function as a `Product` object.
    
- Invalid data normally results in a `422 Unprocessable Entity` response.
    

### Regular Python Class vs. Pydantic Model

|Regular class|Pydantic model|
|---|---|
|Creates objects|Creates objects|
|Does not automatically validate types|Validates data|
|Type annotations are not enforced automatically|Can convert compatible values|

**Remember:** Python classes define objects; Pydantic models define and validate data; FastAPI uses Pydantic for request and response handling.

## Why do we use sql
