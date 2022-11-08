# Why Legend GraphQL

[Legend](https://legend.finos.org/) is our strategic data modelling, transformation, and exploration (query execution) platform. However, as of the current state, most of `Legend` capabilities are on the **read** side: our products consume data already persisted in some datasources. This has been fine so far given that most of our users have their application and data storage infrastructure already in-place and opt-in to use `Legend` as an [OLAP](https://en.wikipedia.org/wiki/Online_analytical_processing)-like solution. We lack the `write` component to make ourselves an `end-to-end` solution. As of the current state, Legend does not support [OLTP](https://www.oracle.com/database/what-is-oltp/) writing to a datasource: the one attempt we have at `write` is [Legend Persistence](https://github.com/finos/legend-engine/tree/master/legend-engine-xt-persistence-component) which is the [ETL](https://www.snowflake.com/guides/what-etl) solution which focuses on batch-writing to data warehouses. This is the motivation for us to start `Legend Write`, the OLTP arm of the platform. Since this is such a vast field, with many advanced toolings already in place, we want to position ourselves right. That is why we have taken a careful look at the current stack and decide to concentrate on supporting full-stack application development use case first. We want to try out with `GraphQL` since its model-first approach to building application aligns well with `Legend`. Hence `Legend GraphQL` comes about.

Following is a quick look at `Legend Persistence` and a _crude_ direction settings for `Legend GraphQL` to dictate what we try to do and try not to do.

## Legend Persistence: A quick glance

The reason why we want to take a quick look at `Legend Persistence` is to check and make sure we could make use of some of the work they have already done.

Persistence currently makes use of [Legend Services](https://github.com/finos/legend-engine/tree/master/legend-engine-language-pure-dsl-service) for **the E (extract) and the T (transform) of ETL**. The L (load) requires further specifications, which comes in form of `sink/ingestion mode`, where the `sink` provides details about the datastore specifications and `ingestion mode` specifies how data would be written to the store. [Persistence write component](https://github.com/finos/legend-engine/tree/master/legend-engine-xt-persistence-component) currently supports fairly advanced features like `bitemporal (milestoning)`, `transaction`... Having said that, since most of these are tailored for batch-writing data, we might not be able to reuse much the logic - considerations are something we can still learn from definitely.

> Persistence, for now, only support relational databases, we are also simplifying the mapping from class to database table, in fact, one of the argument is if this mapping gets too complicated, then perhaps, it means the shape of the store is either too complicated (which might not be the best fit for `OLAP`) or too different from the model, in which case, users should offset this complexity to the service instead. Support for document-based datastores could somewhat be eased due to the fact that we have support for `semi-structured` data in most relational stores.

## Legend GraphQL: What it will be, will not be, and will might be...

The use-case we envision for `Legend GraphQL` goes like this:

- Application developer starts modelling their business domain in `Legend Studio`
- From Studio, they have a way to `deploy/applicationize` their models, starting from a root class (i.e. concept)
- Legend GraphQL will generate the application, including: GraphQL gateway/entry point, implementation of the query validator, resolver and executor: the goal is from client perspective, the GraphQL gateway behave like any other GraphQL endpoint, we can even plug this to some GraphQL explorer tool (e.g. [Apollo Studio Explorer](https://studio.apollographql.com/sandbox)) and it will just work

> Note that, once we enter the realm of application generation, there are many things we could do other than just generating the endpoints, e.g. slapping analytics and elastic search capabilities on top to support free-text search. But that's not the goal here, at least in the first iteration, we aim to do one thing and do it right.

This opens the door for many power features that the tool can/need to provide:

- Auto-provision and setup of databases
- Subscription/notification/live query
- Pagination
- Caching
- Datasource federation
