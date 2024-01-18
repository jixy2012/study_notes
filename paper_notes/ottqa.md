# OTT-QA

question anwsering over tables and text

previously: iterative retriever + BERT reader

## early fusion, fusion retriever
- prealign texts with related tables
	- start by augmenting the table title & info with gpt2, generate queries
	- then, use these queries to search for most related passages with bm25
	- group these together
	- what if multiple tables are related? table -> text to match, what if no tables match the text or vice versa? multiple tables match the text?
- retriever takes top k of these fused blocks of info
- inverse cloze task: given some sentences from a paragraph and a part of a table, which original table & context were they most likely to be from

## cross dependency
- cross attention between blocks
