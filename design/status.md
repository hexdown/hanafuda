# Status

Hanafuda is currently in early design development.

Current next steps are: to define the scope of - and complete a design specification for - an initial prototype.

## Scope

- Define (rust) library APIs
    - Plot API: Initiate a new plot for sowing documents.
    - Sow API: Initiate a new document root.
    - Splice API: Modify document tree by updating cards.
    - Forage API: Retrieve a query set of cards (a "sheaf").
        - by document ID & path
        - by document metadata
        - by card metadata
        - by card text
        - by card embedding vector
    - Delta API: Receive a notification stream of corpus updates.
- Define data models to represent Hexdown ontology
    - Gardeners (peers with permissions to use plot / sow / splice / forage / delta APIs)
    - Orchards / Plots / Arbors (corpus structure)
    - Cards (document content)
        - Scheme (subset of arbor to apply to card text)
        - Index
        - Text
    - Corpus Deltas
        - Till - delta to orchard / plot / arbor
        - Flush - delta to card index / text
    - Vector embedding card indices (root card embeddings represent entire document tree)
    - Metaschema for representing parse tree for Hexdown serialisation as a series of 6-bit 'sips' (between a 4-bit 'nibble' and 8-bit 'byte')
        - Document node metaschema for representing Commonmark Markdown document structure
        - Stem / Blossom metaschema for representing different families of rendered text glyphs
            - initial implementation: Stem / Blossom metaschema for representing English prose in the Latin Modern Roman alphabet
- Define module structure for rust implementation
- Define initial prototype validation flow
    - Additional ingestion layer that uses tree-sitter to ingest markdown documents
    - Additional export layer that converts Hexdown cards to markdown
        - with links to child cards and 
        - tools for navigating a card sheaf returned by a forage query

## Roadmap

- Define vision (in progress)
- Define scope for initial prototype (in progress)
- Define successful flow for initial prototype to be validated by ingesting and exporting markdown documents (todo)
- Define module structure for rust implementation (todo)
- Implement modules (todo)
