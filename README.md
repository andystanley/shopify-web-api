# Shopify Web API
This GraphQL API was modelled after a simple version of the Shopify API.  The project is currently not deployed, as it is expensive to run.  If you would like to see it running, please reach out to me and I would be more than happy to host it for a couple days.  
**Noteable Features:**
* Supports full CRUD operations
* Secured with access keys
* Built with Hasura
* Data is persisted in a PostgreSQL database
* Was originally deployed to a Kubernetes cluster on Google Cloud Platform 
* Has an interactive GraphiQL client

# Documentation
1. [Getting Started](#getting-started)
 * [QuickStart with GraphiQL](#quickstart-with-graphiql)
 * [Using a different Client](#using-a-different-client)
 * [Make your first query](#make-your-first-query)
2. [Using the API](#using-the-api)
 * [Create a shop](#create-a-shop)
 * [Add products to a shop](#add-products-to-a-shop)
 * [Create an order](#create-an-order)
 * [Add line items to an order](#add-line-items-to-an-order)

## Getting Started
### QuickStart with GraphiQL
To start using the API right away, use the interactive GraphiQL environment hosted at ~~http://35.224.16.95/.~~

### Using a different client
If you prefer to use another API client such as Insomnia, please use the following settings:
* Endpoint: ~~http://35.232.2.64/v1alpha1/graphql~~
* Add the following header:  
`"X-Hasura-Access-Key" : "ab654f89"`

### Make your first query
Once you've chosen a client, you can test out the API by running the following query:
```graphql
query {
  shop {
    name
    products {
      name
      quantity
      value
    }
  }
}
```
A successful query should return a similar looking response:
```json
{
  "data": {
    "shop": [
      {
        "name": "Toronto Farmers' Market",
        "products": [
          {
            "quantity": 115,
            "value": 0.5,
            "name": "Tomatoes"
          },
          {
            "quantity": 49,
            "value": 5,
            "name": "Pumpkins"
          }
        ]
      }
    ]
  }
}
```

## Using the API
### Create a Shop
Let's start off by creating a shop.  Your shop id is what you will use to reference your shop later.
```graphql
mutation new_shop {
  insert_shop(
    objects: [
      {
        name: "Nofrills"
      }
    ]
  ) {
    returning {
      id
      name
    }
  }
}	
```
Awesome!  Let's check out our newly created shop by running the following query:
```graphql
query {
  shop(where: {name: {_eq: "Nofrills"}}) {
    id
    name
    products {
      id
      name
      quantity
      value
    }
  }
}
```
A successful query should return a similar looking response:
```json
{
  "data": {
    "shop": [
      {
        "name": "Nofrills",
        "id": 9,
        "products": []
      }
    ]
  }
}
```
Looks like we have a shop, but now we need some products!   

### Add Products to a Shop
Now that we have a shop, let's add some products.  **Use your shop id from the previous query to ensure the new products end up in your shop.**
```graphql
mutation new_products {
  insert_product(
    objects: [
      {
        name: "Cucumbers",
        quantity: 30,
        value: 0.15,
        shop_id: 9
      },
      {
        name: "Apples",
        quantity: 50,
        value: 0.85,
        shop_id: 9
      },
    ]
  ) {
    returning {
      id
      name
      quantity
      value
      shop_id
    }
  }
}
```
Let's run that query again to check out the new products.
```graphql
query {
  shop(where: {name: {_eq: "Nofrills"}}) {
    id,
    name,
    products {
      id
      name
      quantity
      value
    }
  }
}
```
A successful query should return a similar looking response:
```json
{
  "data": {
    "shop": [
      {
        "name": "Nofrills",
        "id": 9,
        "products": [
          {
            "quantity": 30,
            "value": 0.15,
            "name": "Cucumbers",
            "id": 18
          },
          {
            "quantity": 50,
            "value": 0.85,
            "name": "Apples",
            "id": 19
          }
        ]
      }
    ]
  }
}
```
Great!  Now that our store has some products, our customers can place orders.

### Create an order
We've got a store and products.  Let's place our first order.  **Use your shop id from the previous query.**
```graphql
mutation new_order {
  insert_order(
    objects: [
      {
        shop_id: 9
      }
    ]
  ) {
    returning {
      id
      shop_id
    }
  }
}
```
We've created our first order, let's check it out.  
```graphql
query {
  order(where: {shop: {name: {_eq: "Nofrills"}}}) {
    id
    shop_id
    line_items {
      product {
      	name
    	}
      quantity
      value
    }
  }
}
```
A successful query should return a similar looking response:
```json
{
  "data": {
    "order": [
      {
        "line_items": [],
        "shop_id": 9,
        "id": 9
      }
    ]
  }
}
```
Hmmmm, looks like we're missing some line items.  Let's add some.

### Add Line Items to an Order
Let's go ahead and add some line items to our new order.  **Use your order id and product ids from the previous queries to ensure the correct products end up in your order.**
```graphql
mutation new_line_items {
  insert_line_item(
    objects: [
      {
        order_id: 9,
        product_id: 18,
        quantity: 5
      },
      {
        order_id: 9,
        product_id: 19,
        quantity: 5
      }
    ]
  ) {
    returning {
      product_id
      quantity
      value
      order_id
    }
  }
}
```
Let's run that query again to check out the order with our new line items.
```graphql
query {
  order(where: {shop: {name: {_eq: "Nofrills"}}}) {
    id
    shop_id
    line_items {
      product {
      	name
    	}
      quantity
      value
    }
  }
}
```
A successful query should return a similar looking response:
```json
{
  "data": {
    "order": [
      {
        "line_items": [
          {
            "quantity": 5,
            "value": 0.75,
            "product": {
              "name": "Cucumbers"
            }
          },
          {
            "quantity": 5,
            "value": 4.25,
            "product": {
              "name": "Apples"
            }
          }
        ],
        "shop_id": 9,
        "id": 9
      }
    ]
  }
}
```

Great!  You now know how to:
* Create a shop
* Add products to a shop
* Create an order
* Add line items to an order
