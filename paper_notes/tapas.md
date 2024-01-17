# tapas

added additional embeddings to mark tabular structures

predict what cells to select

predict aggregators

masked language model pre-training objective, masks entire cells too

fine tune with question, table, denotation pairs; calculate loss for cell selection, scalar (usually aggregation) and ambiguous anwser

data: wikitq, questions & tables pairs; sqa, decomposed questions from wikitq; wikisql, text to sql