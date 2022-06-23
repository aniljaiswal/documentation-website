---
layout: default
title: Alias
nav_order: 10
has_children: false
parent: Supported field types
---

# Alias field type

An alias field type creates another name for an existing field. You can use aliases in [search](#using-aliases-in-search-api) and [field capabilities](#using-aliases-in-field-capabilities-api) APIs with some [exceptions](#exceptions). To set up an [alias](#alias-field), you need to specify the [original field](#original-fields) name in the `path` parameter.

## Example

```json
PUT movies 
{
    "mappings" : {
        "properties" :  {
            "year" : {
                "type" : "date"
            },
            "release_date" : {
                "type" : "alias",
                "path" : "year"
            }
        }
    }
}
```

## Parameters

Parameter | Description 
:--- | :--- 
`path` | The full path to the original field, including all parent objects. For example, parent.child.field_name. Required.

## Alias field

Alias fields must obey the following rules:

- An alias field can only have one original field.
- In nested objects, the alias must have the same nesting level as the original field.

To change the field that the alias references, update the mappings. Note: aliases in any previously stored percolator queries will still reference the original field.
{: .note }

## Original field

The original field for an alias must obey the following rules:
- The original field must be created before the alias is created.
- The original field cannot be an object or another alias.

## Using aliases in search API

You can use aliases in the following read operations of search:
- Queries
- Sorts
- Aggregations
- `stored_fields`
- `docvalue_fields`
- Suggestions
- Highlights
- Scripts that access field values.

## Using aliases in field capabilities API

To use an alias in field capabilities API, specify it in the fields parameter.

```json
GET movies/_field_caps?fields=release_date
```

## Exceptions

You cannot use aliases in the following situations:
- In write requests, such as update requests.
- In multi-fields or as a target of `copy_to`.
- As a _source parameter for filtering results.
- In APIs that take field names, such as term vectors.
- In `terms`, `more_like_this`, and `geo_shape` queries (aliases are not supported when retrieving documents).

## Wildcards

In search and field capabilities wildcard queries, both the original field and the alias are matched against the wildcard pattern. 

```json
GET movies/_field_caps?fields=release*
```