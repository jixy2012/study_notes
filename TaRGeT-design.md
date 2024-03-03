# TaRGeT Design! 
note: **updates are going to be frequent** as I go along coding & reading literature probably

---

## Datasets

### TaRGeT Dataset
Description: a super class for the other dataset classes in TaRGeT. All other datasets inherit from this class, and it will have the most basic components of a dataset for a TaRGeT.

**TODO**: how strict do we want to be with the formatting of the files? ie headers & types? May affect how easy it is for users to load custom datasets

#### Instance Variables
- `dataset_name`: name of the dataset from which the data comes. also in huggingface loading configs, you can configurate which rows of a data file corresponds to which dataset (if for example there's a huge file combining many datasets contents), so the name will come in handy for that
- `corpus`: a huggingface DatasetDict object that contains train, test, and validation. This is strictly tables (represented by a nested array) and metadata of tables (caption, source, table name, id, etc.)
- `queries`: a huggingface DatasetDict object that contains the same split as `corpus`. This contains the query metadata, query texts, and the table ids of the corresponding correct tables.
- `data_directory`: a optional path to the directory where the data we are working with resides. this is optional, if the user prefers to keep everything in memory, there's no need to fill this out. otherwise, this provides an easier way for the users to specify a working directory path.
- `query_type`: type of queries in this dataset. as seen from literature, different datasets focuses on different downstream tasks and consequently have different queries. this variable provides more context when presenting the results of evaluation runs on this dataset.

#### Functions

- `load`: takes in a parameter `split`, loads the data into `corpus` and `queries` DatasetDict
- `load_corpus`: takes in a parameter `split`, specifically loads the corpus
- `load_queries`: takes in a parameter `split`, specifically loads the queries
- `get_dataset_name`, `get_corpus`, `get_queries`, `get_corpus_header`, `get_queries_header`, etc utility functions.
- `persist_corpus_to`: takes in a `path` and a `format`, save the tables in the corpus to a specific location & format
- `convert_corpus_tables_to`: takes in a format (pandas dataframe, nested array, etc) and conver the tables within the corpus to that particular format. 

### Generic Dataset

Assuming the user is probably loading custom data from local file directory.

#### Additional Fields

- `corpus_path`: path to the corpus file
- `queries_path`: path to the queries file 

#### Additional Functions

- `check_path`: checking if the file path provided is valid and the files exist

### HuggingFace Dataset

#### Additional Fields

- `hf_corpus_repo`: repo name for the corpus file
- `hf_queries_repo`: repo name for the queries file

#### Additional Functions


---


## Table Retriever
The main interface we want the users to implement when using TaRGeT. They **must** implement `retrieve` function, others are optional.

### TaRGeTRetriever

#### Instance Variables
(**TODO**: I think we should leave this up to the users. they decide what to add to be helpful)

#### Functions
- `retrieve`: takes in a `list[queries]`, `dataset_name`, `top_k`, `**kwargs` and returns a `list[tableID]`

### TaRGeTIDONTKNOWWHATTOCALLTHISBUTITHASENCODECORPUS
inherits from `TaRGeTRetriever`

#### Instance Variables
- `expected_corpus_format`: tells the evaluator what format of data that `encode_corpus` function expects. can be nested array, pd dataframe, etc. this way, the evaluator can convert the data format accordingly before passing it into the `encode_corpus` function

#### Functions
- `retrieve`: takes in a `corpus`, `query`, `top_k`,`**kwargs` and returns a `list[tableID]`
- `encode_corpus`: takes in a corpus (type specified by `expected_corpus_format`) and returns a list of embeddings. this will enforce strict output formats to ensure ease of persistence. 


---


## TaRGeT Evaluation
the main evaluation class that runs evaluations for retrieving the correct tables & other downstream tasks. takes in a TaRGeTRetriever object and run the retrieving tasks.

### TaRGeTEvaluator
#### Instance Variables
- `auto_preprocess`: boolean to signify if you want to automatically preprocess the tables by providing a `encode_tables` function in your table retriever implementation. otherwise, the evaluation will not run any preprocessing (be that embedding, flattening, reformatting, persisting, etc. of the tables), and at evaluation time work under the assumptions that will not require a corpus embedding to be passed in, you only needs the query to be passed in to retrieve relevant table ids. defaults to `False`, can auto set this depending if the `retriever` provides a `encode_corpus` function
- `retriever`: a TaRGeTRetriever object. Upon being passed in, the constructor will check if the implementation complies with the `auto_preprocessing` boolean (ie if `auto_preprocessing` is set to `True`, the implementation must include a `encode_corpus` function). 
- `dataset_names`: a list of datasets names (or a single string if only want one dataset) to run retrieval evaluation on. 
- `task_names`: a list of names of the tasks to run

#### Functions

- `load_datasets_for_eval`: loads in the TaRGeT datasets specified in `dataset_names`, store in `datasets` variable. **TODO**: figure out how to efficiently load data esp for larger datasets.
- `auto_preprocess_corpus`: if `auto_preprocessing` is true, use this to encode the corpus. called at evaluation time. probably persist this somewhere so it can be read later.
- `run_eval`: run tasks listed in `task_names` list. Creates the corresponding `Task` object and run the `evaluate` of those tasks. you can optionally pass in a list of `dataset_names` to run evaluation on; if no list of names are provided, default to the instance variable instead. this will call the `auto_preprocess_corpus` if preprocessing is needed. **TODO**: not sure what the evaluation after getting the queries will look like, so prob gonna have to specify here a little more.

---

## Tasks
Class for tasks to run for evaluating the retriever. This includes the table retrieval task, which is a focus for TaRGeT, and downstream tasks after retrieving the tables.

### AbsTask
The superclass of all tasks.

#### Instance Variables
- `task_name`: name of the task
- `corpus_datasets`: a dictionary of TaRGeT datasets only corpus.
- `queries_datasets`: a dictionary of TaRGeT datasets only queries.

#### Functions
- `load_task_datasets`: loads the corresponding dataset for the task
- `evaluate`: runs the specific task and evalute according to the metrics available for that task

### Table Retrieval Task: Retrieving the correct tables.

#### TableRetrievalTask
The main task focus of TaRGeT. This task calls the retriever model and evaluate how accurate the retrieval results are.
##### Instance Variables
- `k_values`: a list of integers, each being a top k threshold

##### Functions
- `run_retriever`: takes in a `TaRGeTRetriever` and run `retrieve` to get the results of running retrieve on the `corpus_datasets` with the queries in `queries_datasets`. 
- `evaluate`: evaluate the results of the retrieval with `k_values` instance variable. **TODO**: Exact metrics for how to evaluate the effectiveness of the retrieval is tbd.

### Downstream Tasks: retrieval-result-to-end tasks
classes for running downstream tasks after table retrieval has been completed. starting point for these tasks is a bunch of retrieved tables. TaRGeT uses the same models/techniques to complete these tasks.

#### AbsDownstreamTask
A superclass for all the specific downstream tasks. Inherits from `AbsTask`

##### Instance Variables
- `relevant_tables`: some tables that are selected by the retriever 

**TODO**: discuss how these downstream tasks can be efficiently implemented? many benchmarks have already implemented these, easiest way to integrate into TaRGeT?

#### Text2SQLTask
inherits from `AbsDownstreamTask`

#### FactVerificationTask

#### TableQATask

#### TableGroundedDialogueGenerationTask

#### DataAnalysisTask