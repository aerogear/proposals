# Sync Admin UI

The Admin UI will be written in React, and use Patternfly for the look & feel.
See https://github.com/patternfly/patternfly-react & https://github.com/patternfly/patternfly-react-demo-app for more info.

## Authentication

As the server will be running on OpenShift, a ServiceAccount Oauth Client will be used to leverage OpenShift as an auth provider.
This follows the pattern in the [Auth proposal here](../auth/developer-single-sign-on-across-mobile-services.md).
Any OpenShift user who has 'edit' access to the the `Deployment` object for the Data Sync server will be able to get access to the Data Sync Admin UI.

## Architecture

The Admin UI will be located in the same repository as the Server. It will use React with JSX and the JSX will be compiled using link:https://reactjs.org/docs/add-react-to-a-website.html#add-jsx-to-a-project[babel]. We want to
avoid the use of heavyweight build tools (Webpack, Grunt, Gulp) as much as possible and rely on npm scripts. For routing the most popular option (for web projects) is link:https://github.com/ReactTraining/react-router[react-router]. We can investigate others but i would suggest to use this library.
As the Admin UI manages quite a lot of state i would also suggest to use link:https://redux.js.org[redux] as a central state management tool.

## Screens

There are 4 main screens in the initial Admin UI:

* Data Schema
* Data Sources
* Resolver Mappings
* Schema Playground

### Data Schema

The link:https://redhat.invisionapp.com/share/4RLVQKKQ2VS#/screens/305421181[Schema Editor] will be used to define the types, mutations and subscriptions of the user's data structure. It will be split in two sections:

. left side: a text editor where the user can edit the source of the schema.
. right side: an graphical (tree) representation of the schema. This will be used to explore the schema, add and edit resolvers. Adding and removing of nodes is not planned for the first version.

A resolver is typically defined for all Queries and Mutations. However, a field level resolver makes sense for a 1:M relationship. For example, given then below schema:

```
type Note {
  id: String
  content: String
  comments: [Comment]
}

type Comment {
  id: String
  text: String
  note_id: String
}
```

a resolver would be attached to the comments field of Note for resolving all Comments that are linked to that Note e.g. execute a SQL query like 'select * from Comments where note_id = "some_id"'

### Data Sources

This link:https://redhat.invisionapp.com/share/4RLVQKKQ2VS#/screens/305423609[view] allows the user to view and manage data sources. Many data sources can be used in a single schema. There will be a button called `Add Data Source` which opens a link:https://redhat.invisionapp.com/share/4RLVQKKQ2VS#/screens/305424878[Pop-up] where the user can specify the type and credentials of the Data source.
Editing and deleting Data sources should also be possible. 

### Resolver Mappings

This section lets the user define queries for data sources and map them to resolvers.

### Resolver editor

A Pop-up shown when the user clicks on `Add Resolver` in the `Data Schema` screen. It is used to combine a data source with queries to retrieve the data and transform the result. For example, there could be a resolver for the `getUser` query that combines the `Postgres` data source with a query `select {{userId}} from users;`.
The view should have a dropdown to select the data source and two fields to edit the:

. `request mapping`: a query to retrieve data.

. `response mapping`: allows to transform the retrieved data into any form the users needs.

There are a couple of use cases where the response mappings are useful:

* being able to map a potentially complex response into a simpler schema

* adding authorisation to filter out items from the response based on the user context

### Schema Playground

A screen where the user can run queries and inspect the results. We should use the default link:https://github.com/graphql/graphiql[Graphiql] view if possible.
Another option would be [Prism](https://github.com/prismagraphql/graphql-playground). This would be worth an investigation.