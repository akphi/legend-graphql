# Landscape Study

This is a brief study about the [GraphQL](https://graphql.org/) tooling landscape. Most existing solutions can be divided _roughly_ into 2 camps: model-first vs. datasource-first.

> This distinction is just a crude approximation, each solution tends to bias one direction over the other, but at the end of the day, each of them provides toolings and mechanisms to allow developers thinking and working in either ways.

## Model-first

This approach starts with designing the domain knowledge models/concepts graph to build up the GraphQL schema specification, which then can be used to setup a GraphQL gateway to support more intuitive querying of data. The work afterwards is to setup the datasource layer (including federation of different datasources), and the query resolver mechanics. This camp includes solutions like [Apollo GraphQL](https://www.apollographql.com/), [Prisma GraphQL](https://www.prisma.io/graphql), etc.

Let's take a closer look at `Apollo GraphQL`

_TBD_

## Datasource-first

This approach starts with thinking about the datasource specifications, or cleaning up existing ones, or consider federating multiple datasources together. _Some solutions even support generating GraphQL SDL code from these specifications (such as `hasura`),_ but the main focus is to expose a GraphQL server where client can fetch the schema, do introspection, query, etc. so maybe there's no real need to even use the GraphQL SDL code at all.

Let's take a look at `hasura`, its written in `Haskell` and first created to integrate natively with `PostgreSQL` (at the moment of this writing, support for document-based datasources is still underway). For a typical `todos/user` schema example, the produced `Mutation` and `Query` schema might look like:

```graphql
Query {
  todos {
    # parameter
    where: {
      _and:
      _or:
      _not:
      _created_at:
      _id: {
        _eq:
        _gt:
        _gte:
        ...
      }
      title
    }
    created_at
    id
    title
    ...
  }
}

Mutation {
  delete_todos {
    where*: {
      ...
    }
    affected_rows
    returning
  }
  delete_todos_by_pk {
    id*:
    title
    ...
  }
  insert_todos {
    objects*: {
      title
    }
    on_conflict: ... # needs more details here
    affected_rows
    returning
  }
  insert_todos_one {
    object*: {
      title
    }
    on_conflict: ...
    title
    ...
  }
  update_todos {
    _set*: {
      title
    }
    where*: {
      ...
    }
    affected_rows
    returning
  }
  update_todos_by_pk {
    _set*: {
      title
    }
    pk_columns*: {
      id*:
    }
    affected_rows
    returning
  }
  update_todos_many {
    updates*: {
      _set*: {
        title
      }
      where*: {
        ...
      }
    }
    affected_rows
    returning
  }
}
```
