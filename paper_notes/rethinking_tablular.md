# Rethinking tabular data

how to make llms preceive structured data better
combing textual and symbolic reasoning for tabular data

transposing a table makes it much harder for models to reason over them, which means llms don't generalize well to tables that are of varied formats

but why use llms to detect and transpose table???

NORM
- read the first row and first col of a table, let llm decide which is more fitting as the header
- ask llm which ordering is best

Mixture of reasoning
- self-consistency, give many answers and pick the most frequent
- self-evaluation, choose between textual and symbolic
- mix self-consistency, use both textual and symbolic then aggregate most likely ans
