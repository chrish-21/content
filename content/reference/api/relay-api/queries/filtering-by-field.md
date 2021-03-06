---
alias: aephaimu5n
path: /docs/reference/relay-api/filtering-by-field
layout: REFERENCE
shorttitle: Filtering
description: Use complex filter conditions to receive only the data you need. Filters are expressed by using GraphQL query arguments.
simple_relay_twin: xookaexai0
tags:
  - relay-api
  - queries
  - filters
  - query-arguments
related:
  further:
    - sa8aiwurae
    - riekooth4o
    - uo6uv0ecoh
  more:
    - cahzai2eur
---

# Filtering by field

When querying all nodes of a type you can supply different parameters to the `filter` argument to filter the query response accordingly. The available options depend on the scalar fields defined on the type in question.

You can also include filters when including related fields in your queries to [traverse your data graph](!alias-uo6uv0ecoh).

## Applying single filters

If you supply exactly one parameter to the `filter` argument, the query response will only contain nodes that fulfill this constraint.

### Filtering by value

The easiest way to filter a query response is by supplying a field value to filter by.


> Query all posts not yet published:

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allPosts(filter: {
      published: false
    }) {
      edges {
        node {
          id
          title
          published
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allPosts": {
        "edges": [
          {
            "node": {
              "id": "cixnen24p33lo0143bexvr52n",
              "title": "My biggest Adventure",
              "published": false
            }
          }
        ]
      }
    }
  }
}
```

### Advanced filter criteria

Depending on the type of the field you want to filter by, you have access to different advanced criteria you can use to filter your query response. See how to [explore available filter criteria](#explore-available-filter-criteria).

> Query all posts whose title are in a given list of titles:

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allPosts(filter: {
      title_in: ["My biggest Adventure", "My latest Hobbies"]
    }) {
      edges {
        node {
          id
          title
          published
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allPosts": {
        "edges": [
          {
            "node": {
              "id": "cixnen24p33lo0143bexvr52n",
              "title": "My biggest Adventure",
              "published": false
            }
          },
          {
            "node": {
              "id": "cixnenqen38mb0134o0jp1svy",
              "title": "My latest Hobbies",
              "published": true
            }
          }
        ]
      }
    }
  }
}
```

Note: you have to supply a *list* as the `<field>_in` argument: `title_in: ["My biggest Adventure", "My latest Hobbies"]`.

### Relation filters

* For to-one relations, you can define conditions on the related node by nesting the according argument in `filter`

> Query all posts of authors with the `USER` access role

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allPosts(filter: {
      author: {
        accessRole: USER
      }
    }) {
      edges {
        node {
          title
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allPosts": {
        "edges": [
          {
            "node": {
              "title": "My biggest Adventure"
            }
          },
          {
            "node": {
              "title": "My latest Hobbies"
            }
          },
          {
            "node": {
              "title": "My great Vacation"
            }
          }
        ]
      }
    }
  }
}
```


* For to-many relations, three additional arguments are available: `every`, `some` and `none`, to define that a condition should match every, some or none related nodes.

> Query all users that have at least one published post

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allUsers(filter: {
      posts_some: {
        published: true
      }
    }) {
      edges {
        node {
          id
          posts {
            edges {
              node {
                published
              }
            }
          }
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allUsers": {
        "edges": [
          {
            "node": {
              "id": "cixnekqnu2ify0134ekw4pox8",
              "posts": {
                "edges": [
                  {
                    "node": {
                      "published": false
                    }
                  },
                  {
                    "node": {
                      "published": true
                    }
                  },
                  {
                    "node": {
                      "published": true
                    }
                  }
                ]
              }
            }
          }
        ]
      }
    }
  }
}
```

* Relation filters are also available in the nested arguments for to-one or to-many relations.

> Query all users that did not like a post of an author in the `ADMIN` access role

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allUsers(filter: {
      likedPosts_none: {
        author: {
          accessRole: ADMIN
        }
      }
    }) {
      edges {
        node {
          name
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allUsers": {
        "edges": [
          {
            "node": {
              "name": "John Doe"
            }
          },
          {
            "node": {
              "name": "Sally Housecoat"
            }
          },
          {
            "node": {
              "name": "Admin"
            }
          }
        ]
      }
    }
  }
}
```

## Combining multiple filters

You can use the filter combinators `OR` and `AND` to create an arbitrary logical combination of filter conditions.

### Using `OR` or `AND`

Let's start with an easy example:

> Query all published posts whose title are in a given list of titles:

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allPosts(filter: {
      AND: [{
        title_in: ["My biggest Adventure", "My latest Hobbies"]
      }, {
        published: true
      }]
    }) {
      edges {
        node {
          id
          title
          published
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allPosts": {
        "edges": [
          {
            "node": {
              "id": "cixnenqen38mb0134o0jp1svy",
              "title": "My latest Hobbies",
              "published": true
            }
          }
        ]
      }
    }
  }
}
```

Note: `OR` and `AND` accept a *list* as input where individual list items have to be wrapped by `{}`: `AND: [{title_in: ["My biggest Adventure", "My latest Hobbies"]}, {published: true}]`

### Arbitrary combination of filters with `AND` and `OR`

You can combine and even nest the filter combinators `AND` and `OR` to create arbitrary logical combination of filter conditions.

> Query all posts that are either published and in a list of given titles, or have the specific id we supply:

```graphql
---
endpoint: https://api.graph.cool/relay/v1/cixne4sn40c7m0122h8fabni1
disabled: false
---
query {
  viewer {
    allPosts(filter: {
      OR: [{
        AND: [{
          title_in: ["My biggest Adventure", "My latest Hobbies"]
        }, {
          published: true
        }]
      }, {
        id: "cixnen24p33lo0143bexvr52n"
      }]
    }) {
      edges {
        node {
          id
          title
          published
        }
      }
    }
  }
}
---
{
  "data": {
    "viewer": {
      "allPosts": {
        "edges": [
          {
            "node": {
              "id": "cixnen24p33lo0143bexvr52n",
              "title": "My biggest Adventure",
              "published": false
            }
          },
          {
            "node": {
              "id": "cixnenqen38mb0134o0jp1svy",
              "title": "My latest Hobbies",
              "published": true
            }
          }
        ]
      }
    }
  }
}
```

Note how we nested the `AND` combinator inside the `OR` combinator, on the same level with the `id` value filter.

## Explore available filter criteria

Apart from the filter combinators `AND` and `OR`, the available filter arguments for a query for all nodes of a type depend on the fields of the type and their types.

Let's consider the following schema:

```graphql
type Meta {
  id: ID!
  text: String!
  number: Int!
  decimal: Float!
  flag: Boolean!
  date: DateTime!
  enum: SomeEnum!
  object: Json!
}
```

Based on this type, a `MetaFilter` type will be generated with the following fields, grouped by field type.

```graphql
input MetaFilter {
  # logical operators
  AND: [MetaFilter!] # combines all passed `MetaFilter` objects with logical AND
  OR: [MetaFilter!] # combines all passed `MetaFilter` objects with logical OR

  # DateTime filters
  createdAt: DateTime # matches all nodes with exact value
  createdAt_not: DateTime # matches all nodes with different value
  createdAt_in: [DateTime!] # matches all nodes with value in the passed list
  createdAt_not_in: [DateTime!] # matches all nodes with value not in the passed list
  createdAt_lt: DateTime # matches all nodes with lesser value
  createdAt_lte: DateTime # matches all nodes with lesser or equal value
  createdAt_gt: DateTime # matches all nodes with greater value
  createdAt_gte: DateTime # matches all nodes with greater or equal value

  # Float filters
  decimal: Float # matches all nodes with exact value
  decimal_not: Float # matches all nodes with different value
  decimal_in: [Float!] # matches all nodes with value in the passed list
  decimal_not_in: [Float!] # matches all nodes with value not in the passed list
  decimal_lt: Float # matches all nodes with lesser value
  decimal_lte: Float # matches all nodes with lesser or equal value
  decimal_gt: Float # matches all nodes with greater value
  decimal_gte: Float # matches all nodes with greater or equal value

  # Enum filters
  enum: META_ENUM # matches all nodes with exact value
  enum_not: META_ENUM # matches all nodes with different value
  enum_in: [META_ENUM!] # matches all nodes with value in the passed list
  enum_not_in: [META_ENUM!] # matches all nodes with value not in the passed list

  # Boolean filters
  flag: Boolean # matches all nodes with exact value
  flag_not: Boolean # matches all nodes with different value

  # ID filters
  id: ID # matches all nodes with exact value
  id_not: ID # matches all nodes with different value
  id_in: [ID!] # matches all nodes with value in the passed list
  id_not_in: [ID!] # matches all nodes with value not in the passed list
  id_lt: ID # matches all nodes with lesser value
  id_lte: ID # matches all nodes with lesser or equal value
  id_gt: ID # matches all nodes with greater value
  id_gte: ID # matches all nodes with greater or equal value
  id_contains: ID # matches all nodes with a value that contains given substring
  id_not_contains: ID # matches all nodes with a value that does not contain given substring
  id_starts_with: ID # matches all nodes with a value that starts with given substring
  id_not_starts_with: ID # matches all nodes with a value that does not start with given substring
  id_ends_with: ID # matches all nodes with a value that ends with given substring
  id_not_ends_with: ID # matches all nodes with a value that does not end with given substring

  # Int filters
  number: Int # matches all nodes with exact value
  number_not: Int # matches all nodes with different value
  number_in: [Int!] # matches all nodes with value in the passed list
  number_not_in: [Int!] # matches all nodes with value not in the passed list
  number_lt: Int # matches all nodes with lesser value
  number_lte: Int # matches all nodes with lesser or equal value
  number_gt: Int # matches all nodes with greater value
  number_gte: Int # matches all nodes with greater or equal value

  # String filters
  text: String # matches all nodes with exact value
  text_not: String # matches all nodes with different value
  text_in: [String!] # matches all nodes with value in the passed list
  text_not_in: [String!] # matches all nodes with value not in the passed list
  text_lt: String # matches all nodes with lesser value
  text_lte: String # matches all nodes with lesser or equal value
  text_gt: String # matches all nodes with greater value
  text_gte: String # matches all nodes with greater or equal value
  text_contains: String # matches all nodes with a value that contains given substring
  text_not_contains: String # matches all nodes with a value that does not contain given substring
  text_starts_with: String # matches all nodes with a value that starts with given substring
  text_not_starts_with: String # matches all nodes with a value that does not start with given substring
  text_ends_with: String # matches all nodes with a value that ends with given substring
  text_not_ends_with: String # matches all nodes with a value that does not end with given substring
}
```

## Limitations

Currently, neither [**scalar list filters**](https://github.com/graphcool/feature-requests/issues/60) nor [**JSON filters**](https://github.com/graphcool/feature-requests/issues/148) are available. Join the discussion in the respective feature requests on GitHub!

If you want to filter a list of strings `tags: [String!]!`:

```graphql
type Item {
  id: ID!
  tags: [String!]!
}
```

you can introduce a new type `Tag` with a single `key: String` field and connect `Item` to `Key` one-to-many or many-to-many:

```graphql
type Item {
  id: ID!
  tags: [Tag!]! @relation(name: "ItemTags")
}

type Tag {
  id: ID!
  key: String!
  item: Item @relation(name: "ItemTags")
}
```

Now you can filter items based on their connected tags using the `tag_none`, `tags_some` and `tags_every` filters.
