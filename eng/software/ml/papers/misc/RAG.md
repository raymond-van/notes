# RAG
Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
https://arxiv.org/pdf/2005.11401
### DPR
- Use LLM to encode documents into d-dimensional vectors, d
- Use another LLM to encode query into d-dimensional vectors, q
- use MIPS (maximum inner-product search)  to find the top-k documents relevant to the query
	- MIPS: max(q^T dot d)
		- the more similar, the larger the dot-product
		- orthogonal vectors have 0 dot product

##### FAISS