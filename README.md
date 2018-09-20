# Shopify Web API
This GraphQL API was built as part of Shopify's Developer Intern Challenge.  This API meets the basic requirements as well as tackling many of the extra credit features including:
* Supporting full CRUD operations
* Securing the API
* Building the API using GraphQL
* Deploying the API to a Kubernetes cluster on Google Cloud Platform  

# Documentation
1. [Getting Started](#getting-started)
 * [Get started with GraphiQL](#get-started-with-graphiql)
 * [Using Your Own Client](#using-your-own-client)
 * [Make your first query](#make-your-first-query)
2. [Using the API](#using-the-api)
 * [Create a shop](#create-a-shop)
 * [Add products to a shop](#add-products-to-a-shop)
 * [Create an order](#create-an-order)
 * [Add line items to an order](#add-line-items-to-an-order)
3. [Extra Info](#extra-info)
 * [Development](#development)
 * [Deployment](#deployment)

## Getting Started
### Get started with GraphiQL
To start using the API right away, use the interactive GraphiQL environment hosted at [http://35.224.16.95/](http://35.224.16.95/).  

### Using your own client
If you prefer to use another API client like Postman or Insomnia, please use the following settings.
* Endpoint: [http://35.232.2.64/v1alpha1/graphql](http://35.232.2.64/v1alpha1/graphql)
* Add the following access key header:  
`X-Hasura-Access-Key : mykey`

### Make your first query
Once you've chosen a client, you can test out the API by running the following query:
```graphql
query {
  shop {
    name,
    products {
      name,
      quantity,
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
Awesome!  Let's check out our newly created shop.
```graphql
query {
  shop {
    name,
    products {
      name,
      quantity,
      value
    }
  }
}
```
Looks like we have a shop, but now we need some products!   

### Add Products to a Shop
Now that we have a shop, let's add some products.  Use your shop id to ensure the new products end up in your shop.
```graphql
mutation new_products {
  insert_product(
    objects: [
      {
        name: "Cucumbers",
        quantity: 30,
        value: 0.15,
        shop_id: 2
      },
      {
        name: "Apples",
        quantity: 50,
        value: 0.85,
        shop_id: 2
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
  shop {
    name,
    products {
      name,
      quantity,
      value
    }
  }
}
```
Great!  Now that our store has some products, our customers can place orders.

### Create an order
We've got a store and products.  Let's place our first order.
```graphql
mutation new_order {
  insert_order(
    objects: [
      {
        shop_id: 2
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
  order {
    id
    shop_id
    line_items {
      quantity
      value
    }
  }
}
```
Hmmmm, looks like we're missing some line items.  Let's add some.
### Add Line Items to an Order
Let's go ahead and add some line items to our new order.
```graphql
mutation new_line_items {
  insert_line_item(
    objects: [
      {
        order_id: 5
        product_id: 14
        quantity: 5,
      },
      {
        order_id: 5
        product_id: 14,
        quantity: 5,
      }
    ]
  ) {
    returning {
      id
      product_id
      quantity
      value
      order_id
    }
  }
}
```
Let's run that query again to check out the order with our new line_items.
```graphql
query {
  order {
    id
    shop_id
    line_items {
      quantity
      value
    }
  }
}
```

Great!  You now know how to:
* Create a shop
* Add products a shop
* Create an order
* Add line items to an order

## Extra Info
### Development
This GraphQL API was developed with [Hasura](https://hasura.io/).  I chose Hasura as it allows rapid API development and easy integration with Docker and Kubernetes.  All data is persisted on a PostgreSQL database on Google Cloud Platform.
### Deployment
The API is deployed as a pod in a Kubernetes cluster on Google Cloud Platform.  The GraphiQL client is deployed as another pod in the same cluster.
