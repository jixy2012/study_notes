solo

data problem?
synthesize training data, with generation questions from input tbales, then serialize tables and questions

address problems: design of self-supervised data discovery, better table representation for the models to understand, find models that learn well from these synthetic data, end-to-end solution, that first retrieves the tables with **high relevancy** (the most crucial part, finding the right tables), then find the actual answer from the tables


## self-supervised data discovery
- generate question-table pairs, question q can be answered with table t
	- generate SQL: sql template, use title (metadata) as context, diverse queries using multiple attributes; discard cols with no header names, discard cells with super long contents
	- have LLMs interpret and generate NL questions of these SQLs
- determine how much training is enough
	- bayesian incremental training? wut

## table representation
- graph
	- basically for each two cells in the same row of a table, you have a tuple of the cell contents + the header of the cells to show relationship (an edge)
	- row-wise complete graph, each two cells in a row has an edge
	- encoding pretty much off the shelf dense representation from a distilled model
- relevance model
	- further annotated triplets to show structure & relationship
	- augmented data by added redundent q triplet pairs
	- model: get feature vectors using an off the shelf QA model, project into a space wrt table info, maxpool for each table, construct multiple equivalent relevance representations, compute relevance with MLP

## dealing with 
- encoding massive amounts of triplet from large tables: knowledge distillation, using smaller student model, and rerank the search results with the teacher model
- storing the triplets: product quantiziation, splitting the vectors up and using new ids

## results
- better at generalizing to new data