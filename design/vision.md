# Vision

Hanafuda is an embedded card store for managing a corpus of [Hexdown](https://github.com/hexdown/spec) documents. This library serves as a building block for the development of tools and applications that work with Hexdown documents. The name "Hanafuda" is derived from the Japanese "flower card" deck as a visual reference to the way Hexdown represents documents as collections of cards featuring blossom glyphs.

For the document model, encoding, arbors, and delta semantics that Hanafuda implements, see the [Hexdown specification](https://github.com/hexdown/spec).

## Role

Hanafuda is the storage layer of the Hexdown ecosystem. It maintains an append-only corpus of Hexdown cards in a local embedded key-value store and exposes a small Rust API for tools and daemons (such as chorognomon) to build on.

Hanafuda is responsible for:

- persisting cards as (face, back) pairs with content-addressed faces and stable card ids
- maintaining secondary indices for fast forage queries by document, metadata, text, and embedding vector
- recording and replaying till and flush deltas
- enforcing gardener permissions on plots and operations
- exposing a notification stream of corpus updates

Hanafuda is **not** responsible for:

- network transport — that is the job of an upstream daemon
- authentication of gardener identity — Hanafuda is given a gardener id and trusts that the upstream daemon verified it
- computing embedding vectors — vectors are supplied by callers, Hanafuda only stores and indexes them
- ingesting or rendering markdown — separate shim crates layer on top of Hanafuda for those tasks
- parsing arbors or card schemes from sources other than their canonical sip serialization — that, too, is an upstream concern

## APIs

The Hanafuda library exposes five top-level APIs:

- **Plot** — initiate a new plot for sowing documents within a gardener's orchard
- **Sow** — initiate a new document root within a plot
- **Splice** — modify a document by inserting, replacing, or removing document nodes within a card
- **Forage** — retrieve a sheaf of cards by query (by document id and path, by document or card metadata, by face text, by embedding vector)
- **Delta** — subscribe to a stream of till and flush deltas as the corpus changes

Concrete Rust signatures for each API are sketched in [api.md](api.md) (TBD).

## Storage

Hanafuda uses an embedded key-value store for persistence — initial choice is [redb](https://github.com/cberner/redb) for its pure-Rust implementation and MVCC properties. See [storage.md](storage.md) (TBD) for the on-disk layout.

Reads are privileged over writes. Hanafuda maintains denormalized secondary indices on write so that forage queries do not require scans.

## Open questions

- Exact crate structure (single crate to start, possible split into spec / store / embed crates later)
- Async surface — sync core with an async surface for the Delta notification stream, or async throughout
- Embedding index implementation (hnsw_rs, instant-distance, or rolling our own over redb)
- How gardener permissions are represented in the store
