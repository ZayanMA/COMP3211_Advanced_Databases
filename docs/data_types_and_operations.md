# Data Types and Operations


## Overview

Advanced DBMSs must support **non-traditional data types** (temporal,
spatial, multimedia) and provide suitable **operations** for querying
them. A key theme: **not every operation makes sense for every type**,
so systems must define meaningful operations (and avoid misleading
ones).

## Data Types

Common types covered:

-   **Numeric** (integers, reals)

-   **Character** (strings)

-   **Temporal** (time-oriented data)

-   **Spatial** (geometric data in 2D/3D)

-   **Text** (documents, semi/unstructured text)

-   **Image** (still images)

-   **Audio & Video** (multimedia streams/files)

## Operations on Data

Typical operation families:

-   **Comparison:** equality, ordering, similarity

-   **Arithmetic:** addition, multiplication, etc.

-   **Fuzzy search / similarity:** approximate matching (especially for
    text/media)

-   **Information retrieval queries:** e.g. documents containing a word;
    images containing a feature

!!! info "Definition"
    __Meaningful operation__: An operation is meaningful when its result has
    a clear, consistent interpretation for that data type. If an operation
    is undefined or ambiguous, it should not be treated as a normal
    operator.

!!! info "Operations: what's meaningful?"
    **Units matter (weights vs numbers):**

    -   $2\,\text{kg} + 2\,\text{kg}$   $\rightarrow$ meaningful (same unit,
        additive quantity)

    -   $2\,\text{kg} \times 2\,\text{kg}$   $\rightarrow$ generally **not
        meaningful** in normal DB semantics (unit becomes kg$^2$)

    -   $13 + 2\,\text{kg}$   $\rightarrow$ meaningless (incompatible kinds)

    -   $13 \times 2\,\text{kg}$   $\rightarrow$ meaningful (scale a
        quantity by a factor)

    **Media types:**

    -   "Compare two images for equality" is usually not meaningful (tiny
        changes break equality).

    -   "Add two images" is ambiguous unless the system defines a specific
        image-processing meaning.

### Ordering: Total vs Partial Orders

A common question for any type is: **is it ordered**, and what kind of
order?

!!! info "Definition"  

    **Total order:** any two values are comparable (for any $a,b$, either
    $a\le b$ or $b\le a$).  
    **Partial order:** some pairs may be incomparable (neither $a\le b$ nor
    $b\le a$ holds).

!!! note "Note"
    Even if you *can* impose an order (e.g. by ID), the key question is
    whether the order has **semantic meaning** or is just a convenience.

## Temporal Data

Temporal data adds the time dimension to support questions such as:

-   Average price of product $X$ during 1995

-   Month with the most copies sold of video $Y$

-   Treatment history of patient $Z$

### Characteristics of Time

Time can differ by:

-   **Structure:** linear; branching time (possible futures); directed
    acyclic graph; periodic/cyclic

-   **Boundedness:** unbounded; bounded with an origin; bounded at both
    ends

### Time Density Models

Slides distinguish time models by how many time points exist between two
points.

  **Model**        Timeline resembles                Ordering property   Points between two points
  ---------------- --------------------------------- ------------------- -----------------------------
  **Discrete**     Integers ($\mathbb{Z}$)           Total order         Finite number of chronons
  **Dense**        Rational numbers ($\mathbb{Q}$)   Partial order       Infinite number of chronons
  **Continuous**   Real numbers ($\mathbb{R}$)       Total order         Infinite number of chronons

!!! info "Definition"
    **Chronon:** the smallest representable time unit (a fixed period) used
    by a system (e.g. 1 second, 1 minute).

### Time Granularity

Granularity = the resolution used when representing time.

!!! info "Example / Intuition"
    Event A at 11:00 and Event B at 15:00 on the same day:

    -   If granularity = **1 day**, A and B occur in the same time unit
        $\Rightarrow$ no precedence is visible.

    -   If granularity = **1 minute**, A precedes B clearly.

!!! note "Note"
    The slides also highlight a distinction between:

    -   **Sequence:** order in which events are recorded/considered

    -   **Time:** actual temporal placement and distance

    These can differ (e.g. logged later vs happened earlier).

### Temporal Data Storage

A database fact/event can have multiple time notions:

-   **Valid time:** when the fact is true in the real world

-   **Transaction time:** when the fact is current/stored in the DB and
    retrievable

-   **Bitemporal:** storing *both* valid and transaction time

!!! info "Example / Intuition"
    A correction scenario (intuition):

    -   A fact could be valid from January, but only inserted into the DB in
        March.

    -   Valid time captures reality; transaction time captures DB history.

## Temporal SQL Extensions (TSQL)

Extensions mentioned:

-   **WHEN clause** (temporal conditions)

-   Timestamp retrieval

-   Retrieval of temporally ordered information

-   `TIME-SLICE` clause to specify a time domain

-   Modified aggregate functions via `GROUP BY`

### The WHEN Clause

Format:
```sql
SELECT {select-list}
FROM {relations}
WHERE {conditions}
WHEN {temporal clause}
```

Temporal comparison operators include:

-   BEFORE / AFTER

-   PRECEDES / FOLLOWS

-   DURING

-   EQUIVALENT

-   ADJACENT

-   OVERLAPS

!!! note "Note"
    These operators relate to **interval reasoning** (as in Allen's Interval
    Calculus): rather than comparing singletimestamps, you compare
    *interval relationships* (overlap, adjacency, containment, etc.).

## Spatial Data

Spatial data represents objects in space.

### Spatial Data Types

Types listed:

-   Points

-   Regions

-   Boxes

-   Quadrangles

-   Polynomial surfaces

-   Vectors

### Spatial Operations

Operations listed:

-   Length / distance (where defined)

-   Intersection

-   Containment

-   Overlap

-   Centre computation

### Applications & Properties

Main application areas:

-   Computer Aided Design (CAD)

-   Computer generated graphics

-   Geographic Information Systems (GIS)

Properties of interest:

-   **Connectivity** (what is linked/connected?)

-   **Adjacency** (what touches what?)

-   **Order** (arrangement/sequence in space)

-   **Metric relations** (distances, angles, areas)

## Why Spatial DB Performance is Hard

-   Objects can be highly complex

-   Data volumes can be very large

-   Real-time constraints may apply

-   Performance is not easy to achieve

-   Often accessed via specialised graphical front-ends (operator skill
    matters)

-   Query processing may not use standard SQL

## Multimedia Data

### Text Data

Text may be:

-   Already machine-readable (word processors, spreadsheets, etc.)

-   Extracted via OCR

Key issue: text is **essentially unstructured** $\Rightarrow$ retrieval
needs an index:

-   Human-built index, or

-   Automatically built **inverted list** (index of significant words
    $\rightarrow$ documents containing them)

!!! info "Definition"
    **Inverted index / inverted list:** maps each word (term) to the set of
    documents that contain it, enabling fast queries like "find all
    documents containing word $w$".

Markup languages add structure:

-   HTML (web)

-   XML / SGML (portable documents with structured data; can define new
    markup languages)

DB support mentioned:

-   **CLOBs** (Character Large Objects) for storing text documents

-   Text search and retrieval facilities

### Document-Style Queries (Motivation)

Typical useful queries:

-   Legal documents concerning client "Jones"

-   Suspects with false teeth who have been interviewed

-   Articles on "databases"

### Image Data

Examples:

-   X-rays, maps, photographs

Storage:

-   Stored as **BLOBs** (Binary Large Objects)

-   No attached semantics by default (the DB stores bits, not meaning)

Image databases need support for:

-   Image analysis and pattern recognition

-   Image structuring and understanding

-   Spatial reasoning and image information retrieval

!!! info "Definition"
    **QBIC (Query By Image Content):** retrieve images using content
    features (e.g. colour/texture/shape) rather than only filenames/labels.

### Audio Data

Digitised audio:

-   Formats: WAV, MP3

-   Consumes large storage; compression commonly used

MIDI:

-   More compact than digitised audio

-   Stored as instruction sequences (e.g. `Note_On`, `Note_Off`,
    `Increase_Volume`)

-   Interpreted by a synthesiser

### Video Data

Video characteristics:

-   Extremely space-hungry

-   Stored as a sequence of frames (each frame can be $>$ 1MB)

-   Playback typically 24--30 fps

Audio-video integration:

-   Interleaved file structures coordinate time sequencing

-   Examples: Microsoft AVI, Apple QuickTime

## Summary - Key Takeaways

-   Different data types $\Rightarrow$ different meaningful operations.

-   Ordering matters: total vs partial order; semantic vs convenience
    order.

-   Temporal: structure, boundedness, density
    (discrete/dense/continuous), granularity, valid vs transaction time.

-   Spatial: specialised types + geometry operations; large/complex data
    makes performance hard.

-   Multimedia:

    -   Text needs indexing (inverted index); markup adds structure
        (HTML/XML).

    -   Images are BLOBs with no semantics unless analysed; QBIC =
        content-based retrieval.

    -   Audio/video are storage-heavy; compression and timing/sync are
        key.
