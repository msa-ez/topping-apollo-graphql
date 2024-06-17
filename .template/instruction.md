#### Easy to Integrate: Apollo is designed to be seamlessly integrated with any JavaScript front-end library, such as React, Vue, or Angular.

#### Rich Developer Tools: Apollo Client comes with a set of developer tools (Apollo DevTools) that make it easier to understand how your GraphQL queries interact with your data.

#### Incremental Adoption: Businesses can start using Apollo Client within parts of their application without having to fully adopt GraphQL throughout.

#### State Management: Apollo Client can also replace traditional state management libraries like Redux, as it provides built-in local state management features.

#### Intelligent Caching: Apollo Client has a smart cache that can automatically track and update the UI with the latest data, helping to reduce unnecessary server requests.

#### Optimistic UI: With Apollo, developers can mock the expected result of a mutation and instantly show the update in the UI, providing users with a responsive experience.

#### Fine-grained Control: Developers have detailed control over GraphQL requests and responses, such as performing polls, retries, and pagination.

#### Strong Community Support: Apollo GraphQL has a thriving and active community. This ensures regular updates, a plethora of third-party libraries, and extensive resources for learning.

#### Full-stack Solution: Apollo offers both client and server solutions, allowing development teams to build an end-to-end, cohesive GraphQL architecture.

#### Flexible and Scalable: Apollo Server supports various data sources, including REST APIs, databases, or even other GraphQL servers. This flexibility ensures that Apollo can scale with application needs.

### How to run

#### 1. Run GraphQL.
```
cd apollo-graphQL
npm install
yarn start
```

#### 2. Write a resolver strategy for subqueries to query composite services.
```
// Example: resolver.js

const resolvers = {
  // Declare the call for object type information (Order, Query, Product) in typeDefs

  Query: {
     //...
  },
  Order: {
      deliveries: (root, args, {dataSources}) => {}

      //  functionName: (parent, args, context, info) => {}
      //  * parent  : The return value of the resolver for the root.
      //  * args    : Arguments or {parameter} values when calling the function.
      //  * context : 
            An object passed to all resolvers executed for a particular operation,
            sharing context like database connections.
          {dataSources}: Calls data connected with xxx-rest-api.js.
      //  * info    : Execution state of the operation, such as field name, path to the field from the root, etc.
  }
}
```

#### 3. Add properties to the type declaration.
```
// Example: typeDefs.js

type Delivery {
    id: Long!
    orderId: Long 
    productId: Long 
    customerId: String 
    deliveryAddress: String 
    deliveryState: String 
    orders: [Order]
    order(orderId: Long): Order
}

type Order {
    id: Long! 
    productId: Long
    customerId: String
    state: String
    deliveries: [Delivery]
    delivery(deliveryId: Long): Delivery
}

// []: Array
//  !: Required field
```

#### 4. Set up the service call functions and call paths.
```
import {RESTDataSource} from 'apollo-datasource-rest';
// apollo-datasource-rest module

class orderRestApi extends RESTDataSource {
    constructor() {
        super();
        this.baseURL = 'http://order:8080';
        // Call address information for the service.
    }

    // functionName() 
    async getOrders() {
        const data = await this.get('/orders', {})
        // URL call information after baseURL.

        var value = this.stringToJson(data);
        // Convert call information from String to Json. 
        
        return value
        // Return call information.
    }

    async getOrder(id) {
        // ...
    }

    stringToJson(str){
        if(typeof str == 'string'){
            str = JSON.parse(str);
        }
        return str;
    }
}
```

#### 5. Proceed with the declaration call mapping.
```
import {ApolloServer} from 'apollo-server';
import resolvers from './graphql/resolvers.js';
import typeDefs from './graphql/typeDefs.js';
import orderRestApi from './restApiServer/order-rest-api.js'
import deliveryRestApi from './restApiServer/delivery-rest-api.js'

const server = new ApolloServer({
    typeDefs,
    resolvers,
    dataSources: () => ({
        orderRestApi: new orderRestApi(),
        deliveryRestApi: new deliveryRestApi()
    }),
    // Declare dataSources to call xxxRestApi information.
});

server.listen({
    port: 8089,
}).then(({url}) => {
    console.log(`🚀  Server ready at ${url}`);
});
```
