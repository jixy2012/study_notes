# My Notes

## potential challenges

- tables have repeated headers (maybe, i'm not super sure), so maybe have to rely much heavier on metadata
 hierarchical structure, with multiple sublevels of table metadata structure?
 - in your experience with relational data, what are some interesting properties and characteristic stuff that may not be obvious to the general eye?

GPT OVV
Tabular Question Answering (Table QA) presents several challenges primarily due to the nature of tabular data and the complexity involved in interpreting and extracting relevant information from it. Here are some of the key challenges:
1. **Understanding Natural Language Queries and Aligning with Tabular Schema**: One of the primary difficulties in Table QA is the need to understand both the user's natural language query and the schema of the table (including column names, table names, and values). This involves translating the query into a format that can be used to retrieve information from the table, such as a SQL query. This translation process can be complex, especially when dealing with queries that require an understanding of the context or involve aggregate operations.
2. **Dealing with Various Table Structures**: Unlike querying a uniform database, tables in real-world scenarios (like web tables or spreadsheets) can vary significantly in structure. This diversity necessitates flexible and adaptable methods for interpreting and extracting information from these tables.
3. **Complex Question Types**: Real-world queries often involve multiple steps and can be non-trivial to answer. For example, a question might require the aggregation of data from different parts of the table or involve conditional logic. Pre-trained models need to be trained on complex and diverse datasets to handle these types of questions effectively.
4. **Incorporating Surrounding Text Context**: For tables accompanied by surrounding text, some methods involve directly extracting or generating answers from the tables and the accompanying text. This adds another layer of complexity as the system needs to understand and correlate information from both the tabular data and its textual context.
5. **Semantic Parsing**: For tables that do not come from a database, semantic parsing remains an important method. However, semantic parsing can be challenging as it requires transforming natural language into structured queries while maintaining the meaning and intent of the original question.
6. **Table Encoding and Reasoning**: Table QA involves challenges related to encoding the table in a way that is useful for question answering and reasoning over the table to find the correct answer. This requires an understanding of the relationships and patterns within the table data.
7. **Retrieval of Relevant Tables in Open-Domain QA**: In open-domain Table QA, identifying and retrieving the relevant tables from a large corpus is a significant challenge. This is particularly difficult when dealing with large datasets or when the relevant data is spread across multiple tables.

Overall, Table QA is a complex task that requires sophisticated methods to accurately interpret and answer queries based on tabular data. The development of effective Table QA systems is an ongoing area of research in the field of natural language processing.

## vector db notes
- need to figure out if any vector db as native structured data support (looks like none), and figure out the important differences, eg advantages and disvantages

## prototyping
- find a tool for extracting schemas & table names from relational databases

## sshing into vm
ssh -i /C:/Users/jixy2/.ssh/cj-gpu-vm-key.pem jixy2012@4.151.71.52

ssh -N -f -L 127.0.0.1:5000:127.0.0.1:5000 -i ~/.ssh/cj-gpu-vm-key.pem jixy2012@4.151.71.52

conda install nvidia/label/cuda-11.5.1::cuda-toolkit
conda install conda-forge::cudatoolkit

 sudo apt install nvidia-cuda-toolkit

 conda install pytorch torchvision torchaudio cudatoolkit -c pytorch -c nvidia


 ## Huggingface stuff
 - there are model configs, how about input configs? why not there yet
 - huggingface dataset tools good for loading in new data.

 MTEB interface: 
 ```
MTEB Interface
class MyModel():
    def encode(self, sentences, batch_size=32, **kwargs):
        """
        Returns a list of embeddings for the given sentences.
        Args:
            sentences (`List[str]`): List of sentences to encode
            batch_size (`int`): Batch size for the encoding

        Returns:
            `List[np.ndarray]` or `List[tensor]`: List of embeddings for the given sentences
        """
        pass

    def encode_queries(self, queries, batch_size=32, **kwargs):
    	"""
    	for queries only
    	"""
    	pass

    def encode_corpus(self, corpus, batch_size=32, **kwargs):
    	"""
    	for corpus/data only
    	"""
    	pass

model = MyModel()
evaluation = MTEB(tasks=["Banking77Classification"])
evaluation.run(model)		


BEIR Interface
class YourCustomDEModel:
    def __init__(self, model_path=None, **kwargs)
        self.model = None # ---> HERE Load your custom model
    
    # Write your own encoding query function (Returns: Query embeddings as numpy array)
    def encode_queries(self, queries: List[str], batch_size: int, **kwargs) -> np.ndarray:
        pass
    
    # Write your own encoding corpus function (Returns: Document embeddings as numpy array)  
    def encode_corpus(self, corpus: List[Dict[str, str]], batch_size: int, **kwargs) -> np.ndarray:
        pass

custom_model = DRES(YourCustomDEModel(model_path="your-custom-model-path"))

class YourCustomCEModel:
    def __init__(self, model_path=None, **kwargs)
        self.model = None # ---> HERE Load your custom model
    
    # Write your own score function, which takes in query-document text pairs and returns the similarity scores
    def predict(self, sentences: List[Tuple[str,str]], batch_size: int, **kwags) -> List[float]:
        pass # return only the list of float scores

reranker = Rerank(YourCustomCEModel(model_path="your-custom-model-path"), batch_size=128)
 ```


## TaRGeT Interface

```
class YourCustomModel:
	def encode_table(self, tables, batch_size=32, **kwargs):
		"""
		encode tables, here it's possible to do any sort of preprocessing
		tables should be a dict? table id to table contents?
		so the output will havev some sort of table id to refer to
		"""

	def encode_queries(self, queries, batch_size=32, **kwargs):
		"""
		encode queries into any sort of processing for search?
		"""

	def preprocess_tables(self, tables, batch_size=32, **kwargs):

	def preprocess_queries(self, queries, batch_size=32, **kwargs):

	def retrieve_tables(self, queries, batch_size=32, top_k=5, **kwargs):

# maybe not needed, only focus on retriever component of each method
	def retrieve_cells(self, queries, batch_size=32, top_k=5, **kwargs):

	def generate_response(self, queries, batch_size=32, top_k=5, **kwargs):
		"""
		maybe? but maybe evaling cells is enough.
		"""
	

```

## TaRGeT classes
```

class TargetDataSet():
    '''
    provide abstraction & connection to existing datasets
    '''

    def __init__(self,
            source_dataset: datasets.Dataset,
            info: datasets.DatasetInfo,
            configs: datasets.BuilderConfig
        ):
    '''
    create a new TargetDataset object. should have the information about the dataset, the configs, tasks supported, and the metrics supported by the dataset
    args:
        source_dataset: a huggingface dataset
        info: dataset info of the related dataset
    '''

    def formatting(self) -> None:
    '''
    formats the huggingface database in a way that conforms with the input
    specified for the user interface. 
    '''

    def test_on_metric(self, *args) -> results:
    '''
    allows the user to input any about of metrics. if the dataset supports these metrics
    run testing on all metrics, return results.
    '''

    def test_on_task(self, args) -> results:
    '''
    input certain tasks to run
    '''

    def load_from_csv() -> datasets.Dataset:
    '''
    loads from csv to huggingface dataset
    '''

class Task():
    '''
    represents the task to execute
    '''
    def execute_task():

class Evaluator():
    '''
    Given the metric and the input output, evaluate the tasks accordingly
    '''

    def evaluate():


```

set up dtr and solo
but very hard to use. it's not very obvious how to input a new table and run these models for retrieval. may need to ask authors for more pointers?
alternatively, should we move to huggingface model that have better documentation for baselines? 

how to evaluate maybe a multi-step approach? currently only one step retrieval is evaluated, so what if there are multiple steps? more interface? configs?


input for DTR -> email author

input for solo?


data representation
