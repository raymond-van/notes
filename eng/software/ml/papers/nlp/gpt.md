#### Helpful links
- https://github.com/karpathy/nanoGPT/
- https://jalammar.github.io/illustrated-gpt2/

# GPT-1
Improving Language Understanding by Generative Pre-Training (OpenAI, 2018)

https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf

https://openai.com/blog/language-unsupervised/

## Summary
- goal is to create a language model that transfers with little adaptation to a wide range of tasks
	- unlike ELMo, makes minimal changes to the architecture to perform specific tasks
		- achieves this by making task-specific changes to the input
- Two-stage training: 
	1. language modeling objective on large corpus of unlabeled data to learn the initial parameters of a neural network model
	2. adapt these parameters to a target task using the corresponding supervised objective
- Transformer architecture
### Unsupervised pretraining
- Uses a variant of Transformer (Transformer Decoder: https://arxiv.org/pdf/1801.10198.pdf) to learn the language model
![](../../images/Pasted%20image%2020221225154231.png)
### Supervised fine-tuning
![](../../images/Pasted%20image%2020221225154900.png)
- fine-tunes ALL weights, unlike ELMo which freezes the pre-trained network and addes additional layers on top
- included language modeling as an auxiliary objective to the fine-tuning helped learning by (a) improving generalization of the supervised model, and (b) accelerating convergence. 
	- thus the loss becomes a multi-loss (Figure 1 left):
	 - ![](../../images/Pasted%20image%2020221225155030.png) where L_1 is the language modelling loss with a labeled dataset as input
### Task-specific input transformation
- For specific task, modify the input into token sequences that can be processed by the pre-trained model
- Doing this allows us to avoid making changes to the architecture (only parameter-tuning)
![](../../images/Pasted%20image%2020221225155201.png)


# GPT-2
Language Models are Unsupervised Multitask Learners (OpenAI, 2019)

https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf

https://openai.com/blog/better-language-models/

## Summary
- transformer-based LM w/ 1.5B parameters trained on 8M webpages (40GB)
	- dataset: used only pages which have been curated/filtered by humans—specifically, used outbound links from Reddit which received at least 3 karma
- direct scale-up of GPT, with more than 10X the parameters and trained on more than 10X the amount of data
- outperforms other language models that were trained on a specific domain
	- GPT-2 zero-shots to state of the art performance on 7 out of 8 tested language modeling datasets
- simple language model training objective: predict the next word in a sequence
	- Zero-shot task-transfer: diversity of the dataset causes this simple goal to contain naturally occurring demonstrations of many tasks across diverse domains allowing it to perform a diverse set of language tasks without the need for task-specific training or labelled datasets (though scores are far from SOTA)
	 - possible because these sub-tasks can be considered a subset of the general language modelling task
![](../../images/Pasted%20image%2020221225172922.png)
- Figure 1: Zero-shot task-transfer performance is far from SOTA
### Input representation
- Parse input string using Byte Pair Encoding (BPE) which is a middle-ground between byte-level encoding and word-level encoding
	- effectively interpolates between word level inputs for frequent symbol sequences and character level inputs for infrequent symbol sequences
	- this approach allows the LM to assign a probability to any Unicode string and to evaluate the LMs on any dataset regardless of pre-processing, tokenization, or vocab size

# GPT-3
Language Models are Few-Shot Learners (OpenAI, 2020)

https://arxiv.org/pdf/2005.14165.pdf

https://openai.com/blog/gpt-3-apps/

## Summary
- GPT scaled up to 175B params
- the larger the model, the better they are at few-shot learning
- few-shot learning:
	- does not do any gradient updates, rather, gives description of the task in the prompt along with demonstrations of the task ?
		- e.g.: for K=2 "Translate from english to french: {demonstration1}, {demonstration2} - {english sample}" ?
- scale enables "in-context learning"


# Instruct-GPT
Training language models to follow instructions with human feedback (OpenAI, 2022)

https://arxiv.org/pdf/2203.02155.pdf

https://openai.com/blog/instruction-following/

## Summary
- By using RLHF, 1.3B param Instruct-GPT performs better than 175B param GPT-3 
	- start with pretrained 1.3B GPT3 LM
![](../../images/Pasted%20image%2020221230151335.png)

# Chat-GPT

https://openai.com/blog/chatgpt/