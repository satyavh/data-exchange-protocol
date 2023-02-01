# 5. Use UUIDs for product footprint ID

Date: 2022-06-02

## Status

Accepted

## Context

Each product footprint has a unique identifier `id`.
In Version 0.8.0, the identifier must be a non-empty string ([Link](https://github.com/sine-fdn/cti-usecase-001/blob/3572d6c0fbd3fb6aaf79965192e7aca21f615d1b/api/openapi-0.8.0.json#L138)).
Version 1.0.0 imposes some restrictions on the non-empty string, inspired by the DID standard ([Link to proposal](https://docs.google.com/document/d/1sYj9IOznmYoZ8k1ZLoXqJrAa2HfgaPOtuROXAwdAPP4/edit#heading=h.u0z5m9hkgni8), [Link to DID specification](https://www.w3.org/TR/did-core/#did-syntax)).
But even with these restriction, there is a very wide range of possible ID values, for example:
- `1`
- `ethanol`
- `ethanol-new-reporting-year`
- `ethanol-new-reporting-year-2`
- `%21%3F_%2F%5E%25`
- `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.

The main purpose of the product footprint ID is to uniquely identify the record.
This proposal is based on the assumption that this should be its _only_ purpose. 
Following this assumption, the very wide range of possible ID values is a weakness and not a strength.
It invites solution providers and/or supply chain actors to "overload" the product footprint ID by trying to make it human-readable or trying to align it with some others kinds of ID (e.g., internal product IDs, GTINs).
Some problems with this are:
- Information that belongs somewhere else (like the `productIds` field) bleeds into the `id` field.
- Users might want to change the ID when the overloaded information changes (e.g., when the product is renamed). Unique record identifiers, however, should be immutable.
- Letting users choose the ID increases mental load and has a high risk of choosing poor identifiers and accidentally using the same ID for different records.
- There is a high likelihood of duplicate IDs across different data owners (such as `ethanol`), which makes debugging and error exploration on the network more difficult.

## Decision

The product footprint `id` MUST be a UUID v4 as specified in [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122) and MUST NOT be changed after record creation.

Non-normative: The sole purpose of the `id` field is to uniquely identify a product footprint record within the set of the data owner's product footprint records.
The content of the ID is meaningless outside of the context of the Pathfinder network.
It should be automatically generated by the solution and by default should not be displayed to the user.

## Consequences

Each product footprint record has an immutable ID in the same format.
This avoids consistency issues in the network that come with mutable IDs, protects users from choosing poor IDs, simplifies debugging and error exploration, and ensures that the ID is not unnecessarily overloaded with additional information that belongs in other places.