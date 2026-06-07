# Spec: `retrieve()`

**File:** `retriever.py`
**Status:** Spec incomplete — fill in all blank fields before implementing

---

## Purpose

Given a user's natural language query, find the most relevant chunks from the vector store using semantic similarity search. Return them ranked by relevance so that `generate_response()` can use them as context.

---

## Input / Output Contract

**Inputs:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `query` | `str` | The user's natural language question |
| `n_results` | `int` | Maximum number of chunks to return (default: `N_RESULTS` from `config.py`) |

**Output:** `list[dict]`

Each dict in the returned list must contain exactly these keys:

| Key | Type | Description |
|-----|------|-------------|
| `"text"` | `str` | The chunk text |
| `"game"` | `str` | The game name this chunk came from |
| `"distance"` | `float` | Cosine distance score — lower means more similar to the query |

Results should be ordered from most to least relevant (lowest to highest distance). Returns an empty list `[]` if the collection contains no documents.

---

## Design Decisions

*Complete the fields below before writing any code. Use your AI tool in Plan or Ask mode to help you reason through what belongs here — but the decisions are yours.*

---

### Query approach

*Describe how you will use `_collection.query()` to find relevant chunks. What arguments will you pass, and why?*

```
[your answer here]
```

---

### Return structure

*Sketch out what one item in your return list looks like as a concrete example. Where does each field come from in the query results?*

```
One example is given below:
[
    {
        "text": "The king can move just one step",
        "game": "Catan",
        "distance": 0.3
    }
]
Each field comes from the ChromaDB. It returns a parallel list of document, metadatas and distanaces of chunks closest to the user query.
```

---

### Handling the nested result structure

*`_collection.query()` returns nested lists. Describe what index you need to access to get the actual list of results for a single query, and why the nesting exists.*

```
You need to access index [0] for each result type: documents[0], metadatas[0], distances[0].

The nesting exists because ChromaDB's query() method accepts multiple queries at once and returns results for all of them. Since each result type is a list of lists (one inner list per query), you get nested lists even when querying with just one query string. With only one query, accessing [0] unwraps the first (and only) set of results to give you the actual chunks and their metadata.
```

---

### Relevance threshold

*Will you filter out results above a certain distance score, or return all `n_results` regardless of how relevant they are? What are the tradeoffs of each approach?*

```
Return all n_results regardless of distance score.

Tradeoff of filtering by threshold:
  Pro: Only includes high-quality, relevant chunks; avoids misleading weak matches
  Con: Might return 0 results if nothing passes the threshold, leaving generate_response() with no context

Tradeoff of returning all n_results:
  Pro: Always has some context to work with; lets generate_response() decide relevance
  Con: Poor matches could add noise; less grounded answers if bad results are used

For a rules bot, always having context is safer than returning nothing. The generate_response() function can then filter or warn about low-relevance results if needed.
```

---

### Edge cases

*How does your implementation behave when: (a) the collection is empty, (b) the query matches no chunks well, (c) the query matches chunks from multiple games?*

```
[your answer here]
```

---

## Implementation Notes

*Fill this in after implementing, before moving to Milestone 3.*

**Test query and top result returned:**

```
Query: [your test query]
Top result game: [game name]
Distance score: [score]
Does it make sense? [yes / no / explain]
```

**One thing about the query results that surprised you:**

```
[your answer here]
```
