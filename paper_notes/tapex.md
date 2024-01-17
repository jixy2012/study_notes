tapex

tasks: table question answering; table fact verification

fine tuning: BART, encoder-decoder architecture, pre-trained with MLM pre-training obj; input the NL task + flattened table; output the select of cells or classification for FV; strategies: vanilla, and intermediate downstream tasks and then target tasks

pre-training: training LM to act like sql executor; supervised, concacnates sql query and flattened table, train on loss to the actual output by a sql engine. correct execution implies high level of understanding of the structured data; data: table from high quality sources, only 1500(!), queries are sampled with set templates and randomly selected headers

larger table hard
