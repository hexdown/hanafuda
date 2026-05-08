# Hanafuda Design Docs

A collection of design documents for the Hanafuda project.

## Vision

The Hanafuda library module maintains an append-only corpus of Hexdown cards in a local kv store. This library supports the development of different tools and applications that work with Hexdown documents.

A Hexdown document macrostructure is composed of a tree of cards. Each card describes a microstructure of root, branch, stem and blossom document nodes. Blossom nodes describe glyphs and other visual elements rendered as part of the document.

Each card combines three components:
- A scheme defining a valid abstract syntax tree for text nodes
- A text describing a fragment of a serialized document node tree
- An index that lists
    - A unique identifier for the card
    - The scheme of the card by scheme path
    - A reference to the text of the card by content hash
    - A list of references to child cards

Document trees are organized into plots by scheme and intent. For example, three plots that could be found in an orchard might be:
- A 'Direct Messages' plot containing messages formatted with the 'core->post' scheme
- A 'Praxis' plot containing essays on how to move thru the day formatted with the 'core->essay' scheme
- A 'Cases' plot containing case studies on author's attempts to apply praxis with the 'core->report' scheme

As an append-only store, Hexdown also provides structure for representing change over time. Deltas are used to represent changes to the corpus over time, with 'Till' delta nodes representing changes to plots and schemes and 'Flush' delta nodes representing changes to the cards of individual document trees.

## APIs

The Hanafuda library exposes the following APIs:

- Plot API: Initiate a new plot for sowing documents.
- Sow API: Initiate a new document root.
- Splice API: Modify document tree by updating cards.
- Forage API: Retrieve a query set of cards.
- Delta API: Receive a notification stream of corpus updates.

Changes are synchronized to disk by the underlying kv store.


## Glyph Encoding

Hexdown character glyphs are each encoded as a sequence of 6 bits that map to the integer values from 0 to 63.

Hexdown supports the alphanumeric subset of utf-8 characters, `[0-9a-zA-Z]` as well as a 'beat' word separator character `-` and a possessive marking character `*`.

All other punctuation and symbols are represented a stem document nodes instead of blossom character glyphs.

All Hexdown data is serialized as a sequence of 6-bit glyphs describing a tree of nodes. The kind of each node is defined by the first glyph in the sequence. Fixed-length nodes are defined by the schema to contain a certain number of child nodes and variable-length nodes can contain 1-64 child nodes as described by the second glyph in the node subsequence. The first glyph of each child node subsequence is the kind of the child node.

## Document Nodes

The Hexdown document nodes are a superset of the Markdown document nodes defined by the [CommonMark](https://commonmark.org/) specification to support ingesting Markdown documents into the Hexdown document model.

## Core Schemes

Schemes define valid chains of document nodes from the root node of the document tree. A root scheme defines the valid document structure for a given document type, while card schemes within each root scheme define the valid content types for each card node.

Hexdown provides several built-in root schemes for common document types, including report, graph, and table. Custom root schemes can be defined to extend a Hexdown system with additional document types.

The Hexdown Metascheme describes the serialization for:
- schemes
- indices
- deltas
- API requests and responses

### Root schemes

- report : book > chapter > section
- graph : space > (directed) edge | node
- table : sheet > column | row > cell

### Card schemes

- passage (markdown) 
- diagram (svg?)
- photo (raster bitmap / 2d splat?)

### Passage scheme

- list : ( neem quant code handle socket wiki hyper quote figure )
- point : ( neem quant mark handle socket wiki hyper quote figure )
- phrase : ( neem quant mark handle socket note wiki hyper figure )
- statement : ( phrase neem quant mark handle socket note wiki hyper figure )
- question : ( phrase neem quant mark handle socket note wiki hyper figure )
- quote : mark : - offset : - breadth
- note : ( phrase statement question quote )
- paragraph : ( phrase statement question note quote list point )

### Report scheme

- banner : - title : - slug
- section : - banner : ( paragraph )
- chapter : - banner : ( section )
- book : - banner : ( chapter )
- root : mark : - <title>( neem ) : - slug : <contributors>( handle ) : ( book chapter section )
