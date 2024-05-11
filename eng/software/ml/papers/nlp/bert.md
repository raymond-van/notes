BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)

https://arxiv.org/pdf/1810.04805.pdf

Note: "Bidirectional Transformer" = "Transformer Encoder" and Left-context-only (Autoregressive) Transformer = "Transformer Decoder" (seen in GPT, can only attend to tokens on left and only be used for text generation, can't fill in blanks (MLM))
- OG Transformer is an Encoder-Decoder architecture because one of the attention layers takes as input, vectors from encoder as well as from the previous layer in decoder
- https://ai.stackexchange.com/questions/27038/why-does-gpt-2-exclude-the-transformer-encoder#:~:text=2-,GPT%2D2%20does%20not%20require%20the%20encoder%20part%20of%20the,representation%20of%20the%20input%20sequence.
	- "The cases when we use encoder-decoder architectures are typically when we are mapping one type of sequence to another type of sequence, e.g. translating French to English or in the case of a chatbot taking a dialogue context and producing a response. In these cases, there are qualitative differences between the inputs and outputs so that it makes sense to use different weights for them"
- Moreover, GPT is auto-regressive whereas BERT is not
	- this is tradeoff, in losing auto-regression, BERT has the ability to incorporate the context on both sides of a words to fill in the blanks

## Summary
- BERT = Bidirectional Encoder Representations from Transformers
	- Architecture: Bidirectional Transformer
	- 340M params
- Noted that there are two existing strategies for applying pre-trained language representations to downstream tasks: 
	- feature-based, e.g. ELMo
	- fine-tuning, e.g. GPT
		 - have advantage of not needing to learn params from scratch
- like ELMo, GPT1, a pretrained unsupervised language model that is fine-tuned in a supervised manner for downstream tasks
	- unlike ELMo, only one additional layer is added on top of the pretrained network for fine tuning
	- unlike GPT, does bidirectional encoding
	 - problem with ELMo and GPT is that their LMs are unidirectional (ELMo uses biLM, a shallow concatenation of independently trained bidirectional LMs)
 - uses a “masked language model” (MLM) unsupervised pre-training objective
	- masked language model randomly masks some of the tokens from the input, and the objective is to predict the original vocabulary id of the masked word based on the context
	- enables the representation to fuse the left and the right context, which allows us to pretrain a deep bidirectional Transformer 
 - the pretrained transformer also has a "next sentence prediction" (NSP) task
	 - thus has both word
 
## BERT
![](../../images/Pasted%20image%2020221225194032.png)
- minimal difference between pretrained architecture and downstream architectures
### Input representation
![](../../images/Pasted%20image%2020221225201717.png)
- Token embeddings are WordPiece embeddings
- Segmentation embeddings indicate if a token belongs to sentence A vs sentence B
- Position embeddings are the embeddings needed for transformer architectures
### Pretraining Task 1: Masked LM
- to train a deep bidirectional representation, simply mask some percentage of the input tokens at random, and then predict those masked tokens
	- MLM sometimes referred to as "Cloze" in literature
 - the final hidden vectors corresponding to the [MASK] tokens are fed into an output softmax over the vocabulary, as in a standard LM
### Pretraining Task 2: Next-sentence prediction
- added second task in pretraining stage because many important downstream tasks such as Question Answering (QA) and Natural Language Inference (NLI) are based on understanding the relationship between two sentences, which is not directly captured by language modeling
- when choosing the sentences A and B for each pretraining example, 50% of the time B is the actual next sentence that follows A (labeled as IsNext), and 50% of the time it is a random sentence from the corpus (labeled as NotNext)
	- C is a vector that can be put into binary classifier (next=true/false)
### Finetuning
- simply plug in the task-specific inputs and outputs into BERT and finetune all the parameters end-to-end
- Figure 1: At the output, the token representations are fed into an output layer for tokenlevel tasks, such as sequence tagging or question answering, and the [CLS] representation is fed into an output layer for classification, such as entailment or sentiment analysis.