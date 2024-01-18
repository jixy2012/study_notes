# gorilla

## data collection
- apis from huggingface, torchhub, and tensorflow hub, json object with fields describing usage
- use gpt4 to generate synthetic instructions for each api call

## finetuning 
- trained with awareness of api call retrieval in the instruction set (doesn't always help)
- additional retrieval db can be included