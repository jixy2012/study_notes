 # llamaindex notes

## Data Storage
### Document
- an entire file
- metadata: dictionary of annotations
- relationships: dictionary of relationships with others
```
from llama_index import Document, VectorStoreIndex

text_list = [text1, text2, ...]
documents = [Document(text=t) for t in text_list]

# build index
index = VectorStoreIndex.from_documents(documents)
```

### Node
- a chunk of file
- metadata & relationships
```
from llama_index.node_parser import SentenceSplitter

# load documents
...

# parse nodes
parser = SentenceSplitter()
nodes = parser.get_nodes_from_documents(documents)

# build index
index = VectorStoreIndex(nodes)
```

Can build vector index over node & document

## Transformations
- more general, takes nodes, spits out nodes with some sort of operation done on them.

## loading in data

### Node Parser
- takes list of documents, turn them into nodes
- you can specify chunk size and chunk overlap
- file based: full files, gets separated by structure, like html tag or md headers;
- text-splitters: turns text strings into nodes, you can also use the nodes from the file based parsers, throw them in here for further chunking with upper limit on chunk sizes.
- relational: some sort of hierarchy for the nodes, not sure why maybe for further contextualization purposes. **NOT** for relational data, still unstructured data
- common workflow:
<pre>
```
1. FlatReader to read in data from raw files; flat reader gives you the file type.
2. use SimpleFileNodeParser, chooses the best parser for the file type
3. if further chunking is needed, use text splitters
 ```
</pre>



## Ingestion Pipeline
- a series of `Transformations`
- gives you nodes, returned directly or stored to a vector store
- kind of like mongodb aggregation?