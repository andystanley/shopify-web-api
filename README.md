# Shopify Web API Documentation


## Getting Started
Head on over to https://my-api.com/

## Create a Shop
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

## Add Products to your Shop
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

## Create an order
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
## Add Line Items to an Order
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

Great!  We've now learned how to
* Create our own shop
* Add products to our shop
* Create an order
* Add items to our order
