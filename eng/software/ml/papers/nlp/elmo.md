Deep contextualized word representations (Peters et al., 2018)

https://arxiv.org/pdf/1802.05365.pdf

## Summary
- ELMo = (word) Embeddings from Language Models
- word vectors should model both (1) complex characteristics of word use (e.g., syntax and semantics), and (2) how these uses vary across linguistic contexts (i.e., to model polysemy)
- previous approaches for learning word vectors only allow a single context-independent representation for each word
- use vectors derived from a bidirectional LSTM that is trained with a self-supervised language model objective on a large text corpus
- ELMo representations differ from other word vectors in that each token is assigned a representation that is a function of the entire input sentence
- ELMo representations are deep, in the sense that they are a function of all of the internal layers of the biLM
	- More specifically, ELMo learns a linear combination of the word vectors stacked above each input word for each end task
- higher-level LSTM states capture context-dependent aspects of word meaning (e.g., they can be used without modification to perform well on supervised word sense disambiguation tasks) while lowerlevel states model aspects of syntax (e.g., they can be used to do part-of-speech tagging)
- after pretraining the biLM with unlabeled data, we **fix the weights** and add additional task specific model capacity, allowing us to leverage large, rich and universal biLM representations for cases where downstream training data size dictates a smaller supervised model 
- Refer to 3.1 for description for info on the bidirectional LSTM

## ELMo 
- first compute context-independent word embedding x_k THEN pass it into LSTM layers of biLM
- computes task-specific word representations by taking a linear combination of the intermediate layer-representations in the biLM (not just top layer like previous architectures)

![](../../images/Pasted%20image%2020221225132051.png)
- To add ELMo to the task-specific supervised model, we first freeze the weights of the biLM and then concatenate the ELMo vector with the word-embedding and pass the ELMo enhanced representation  ([x_k; ELMo_k]) into the supervised model