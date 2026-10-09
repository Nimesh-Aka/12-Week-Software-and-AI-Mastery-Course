Retrieval Augmented Generation

## What is RAG
We have the LLMs now. It has the knowledge to answer questions that related to the its own trained data. But when we ask a question that related to our company policy that not publish to anywhere or the LLMs were not trained by them. So in this kind of scenario LLM will cant answer. For this problem we make RAG.

![[Pasted image 20260926202309.png]]
## How RAG Works

## RAG Questions
#### Fundamentals

1. ⭐ What is RAG, and what problem does it solve?
We have the LLMs now. It has the knowledge to answer questions that related to the its own trained data. But when we ask a question that related to our company policy that not publish to anywhere or the LLMs were not trained by them. So in this kind of scenario LLM will cant answer. For this problem we make RAG.
2. ⭐ RAG vs fine-tuning: when would you use each?
If we want to change the tone of the LLMs outputs, we fine tune a LLM. But fine tune is expensive and takes time to for a small update of the dataset.
We use RAG for changing datasets. we can keep the tone by prompt engineering if want the tone.
Easy to build

3. Why not just put all the documents into the LLM's context window?
In a company has millions of documents, it will definitely out the context window of any current LLM model. 

The highest context window model are between 1M+ and below 10M (Llama has 10 million context window model). A token is roughly 0.75 words then the 1M words are 2000 pages of text. If the company has the 500 pages of text. We can send them and get the answers, but its cost high.
For a small query we lost Millions of tokens

4. Walk me through the offline and online phases of a RAG pipeline.

### Online RAG (Indexing)
Document -> Chucking -> Embedding -> VectorDB
### Offline RAG (Per Question)
Embed the question with same embedding model ->Search the top k (10-20) chunks using cosin similarity -> Reran the best 2-5 chunks -> Build the prompt with question + instructions + relevant chunks -> Generate with LLM

At sometimes we called Online RAG as everything is online and offline as everything is locally running for better data security


#### Chunking and embeddings

![[Pasted image 20260926205125.png]]


5. ⭐ How did you choose your chunk size? What happens if chunks are too small or too large?
If the chunks are too small we miss the context, when chunks are too large we get the "Lost in the middle problem", LLM cant point the exact point that needed.

> 200–300 tokens	Short, precise facts
> 400–600 tokens	General technical documents
> 700–1000 tokens	Detailed explanations
> 1000+ tokens	Large contextual sections

5. Why use overlap between chunks?

When we split the chunks very important data could divide into two chunks. But using the overlap we can prevent that

> A common starting point is around **10–20% overlap**.
> 
> **Overlap is not always necessary.**
> If you're doing **structure-aware/semantic chunking**, where you split at headings, paragraphs, sections, etc., you may need little or no overlap.

5. What is an embedding? How is one produced from text?

**Embeddings are numerical representation of meaning.** 

"The server uses OAuth 2.0."
	V
["The", " server", " uses", " OAuth", " 2.0", "."] by tokenizer
	 V
 The       → vector
server    → vector
uses      → vector
OAuth     → vector
2.0       → vector
	 V
"The server uses OAuth 2.0."
                    ↓
              Embedding model
                    ↓
       [0.12, -0.44, 0.73, ...]


6. Why must the query and the documents use the same embedding model?
Because the **query vector and document vectors need to live in the same vector space** for similarity search to be meaningful.

7. How would you pick an embedding model? (dimensions, domain, speed, max input length)

Dimension -  The embedding model converts text into a vector with a fixed number of dimensions.
(Large one is good for more information but its need more storage, more memory, and we llbit slow)

Domain - We need to check the correct domain embedding model, for coding , general purpose  the embedding model must be different

Speed - This will varient with our resources, (GPU/CPU), need to find fast one
Max input length - if the max input is low we need to re do the chunking

#### Retrieval

10. ⭐ Cosine similarity vs dot product vs Euclidean distance: what's the difference?
These three are **ways of measuring how close two vectors are**.
 
#### Cosine Similarity
Cosine similarity measures the angle/direction between two vectors.

$$ \text{cosine similarity}(A,B)=\frac{A\cdot B}{\|A\|\|B\|} $$

Are the two vectors pointing in the same direction?
Values are usually between -1 and 1:

+1  → same direction / very similar
 0  → unrelated / perpendicular
-1  → opposite direction

#### Dot product
Dot product measures both alignment AND magnitude.
#### Euclidean Distance
Euclidean distance measures the straight-line distance between two points.

$$ d(A,B)=\sqrt{(A_1-B_1)^2+(A_2-B_2)^2} $$


10. **⭐ What is hybrid search, and why would pure vector search fail? (e.g. error codes, IDs)**

"Hybrid search combines lexical search, such as BM25, with semantic vector search. Vector search is good at understanding meaning and handling paraphrases, but it can struggle with exact terms such as error codes, product IDs, document numbers, or technical identifiers. Lexical search handles those exact matches well. Combining both gives us better retrieval coverage than relying on either method alone."

11. **Bi-encoder vs cross-encoder: why use a reranker?**

The initial bi-encoder retrieval is fast and scalable, but it may return semantically similar documents that aren't actually the most relevant. A cross-encoder reranker evaluates the query and retrieved documents together, giving a more accurate relevance score. So we first use a bi-encoder to retrieve a larger candidate set, then use a cross-encoder to rerank the top candidates before sending the best chunks to the LLM.


12. How do you choose k? What's the trade-off?
I wouldn't choose k arbitrarily. I'd evaluate several values on a representative evaluation dataset using metrics such as Recall@k, MRR or NDCG, along with answer quality, latency, and token cost. A larger k generally improves recall but introduces more irrelevant context and increases latency and cost. If I'm using a reranker, I might retrieve a larger candidate set, such as 20–50 chunks, then rerank and pass only the top few chunks to the LLM.

13. How does a vector DB search millions of vectors fast? (ANN, HNSW)
Instead of comparing the query against every vector using brute-force search, vector databases typically use Approximate Nearest Neighbor indexes. One popular approach is HNSW, which organizes vectors into a navigable graph with multiple layers. The search starts at a sparse upper layer, makes large jumps toward the relevant region, then searches more locally in lower layers. This greatly reduces the number of vectors that need to be examined, giving much lower latency at the cost of potentially missing the exact nearest neighbors.


14. How do you handle a follow-up question like "what about for enterprise?" (query rewriting)
I'd use conversational query rewriting. The system takes the current question together with relevant conversation history and rewrites an ambiguous follow-up into a standalone query. For example, if the user first asks about a product's security features and then asks 'what about for enterprise?', the rewriter could produce 'What enterprise security features does this product provide?'. We then embed and retrieve using the rewritten query. The original conversation can still be passed to the final LLM for conversational context.
#### Evaluation

16. ⭐ How did you evaluate your RAG system?


                Evaluation Dataset
                       │
                       ▼
                  User Question
                       │
              ┌────────┴────────┐
              ▼                 ▼
         Retrieval          Generation
              │                 │
              ▼                 ▼
         Recall@K          Correctness
         MRR/NDCG          Faithfulness
         Precision@K       Relevance
                           Abstention
              │                 │
              └────────┬────────┘
                       ▼
                 Overall Quality

I evaluated the retrieval and generation stages separately. First, I created a representative evaluation dataset containing questions with known relevant documents and expected answers. For retrieval, I measured metrics such as Recall@K, MRR and Precision@K to determine whether the relevant chunks were being retrieved. For generation, I evaluated answer correctness, relevance and faithfulness to the retrieved context, and I also tested questions where the answer wasn't present to measure hallucination and abstention behavior. Finally, I compared different configurations such as chunk size, embedding model, k, hybrid search and reranking while also measuring latency and cost.

17. ⭐ What are precision@k and recall@k? Which matters more for RAG, and why?

![[Pasted image 20260927003658.png|320]]
So precision is about **avoiding irrelevant results**.

![[Pasted image 20260927003814.png|322]]
So recall is about **not missing relevant information**.

18. **What is groundedness (faithfulness)? How do you measure it?**
Groundedness means: "Is the LLM's answer actually supported by the retrieved context?"

Method 1 — Human evaluation
Method 2 --  LLM-as-a-judge 

Groundedness, also called faithfulness, measures whether the claims in the generated answer are supported by the retrieved context. I would evaluate it by breaking the answer into factual claims and checking whether each claim is supported by the retrieved documents, either through human evaluation or a validated LLM-as-a-judge approach. For example, if two out of three claims are supported by the retrieved context, the faithfulness score would be 2/3. I would evaluate this separately from answer correctness and relevance, because an answer can be relevant but unsupported, or factually correct but not grounded in the retrieved context.

19. How do you build a test set when you don't have labeled data?
When I don't have labeled data, I create an evaluation dataset from real user queries, documentation, and synthetic questions generated from the documents. Then I manually validate a sample and define expected answers/relevant chunks. I use this dataset to measure retrieval metrics like Recall@K and generation metrics like correctness and faithfulness.

20. What is MRR, and when is it better than precision@k?
"**MRR (Mean Reciprocal Rank)** measures the reciprocal rank of the first relevant result and averages it across queries. It's useful when we care about how high the first relevant document appears. Precision@K measures the proportion of relevant documents within the top K, while MRR focuses specifically on the first relevant result.
#### Hallucination and generation

21. ⭐ Your RAG gives a wrong answer. How do you debug it? (retrieval problem vs generation problem)
22. How do you stop the model from answering when the context doesn't contain the answer?
23. What is the "lost in the middle" problem?

#### Production and system design

24. **⭐ How would you keep the index up to date when documents change?**
I debug RAG failures in two stages. First, I inspect the retrieved chunks. If the required information wasn't retrieved, it's a retrieval problem, so I investigate chunking, embeddings, k, query rewriting, hybrid search, filters, and reranking. If the correct information was retrieved but the LLM still gives a wrong or unsupported answer, it's a generation or grounding problem, so I inspect the prompt, context formatting, conflicting information, and model behavior. This separation helps identify whether I need to improve retrieval or generation.


25. How do you handle access control, so users only see documents they're allowed to see?
"I would enforce authorization at the retrieval layer. During ingestion, I attach ACL (Access Control Lists)or permission metadata to documents/chunks. At query time, I authenticate the user, obtain their roles or permissions, and apply those as metadata filters during vector or hybrid search. This ensures unauthorized documents never reach the reranker or LLM. I would also test different user roles and make sure caching and conversation history don't leak data."


26. How would you reduce latency and cost?
I would optimize both retrieval and generation. For retrieval, I'd use ANN **(Approximate Nearest Neighbor)** indexing, efficient embeddings, and tune k and reranking candidates. For generation, I'd minimize the context sent to the LLM and use the smallest model that meets the quality requirement. I'd also use caching, batching, and streaming where appropriate. Finally, I'd measure latency, token usage, cost, and answer quality before and after each optimization.

27. **How do you handle tables, PDFs, and images?**
I handle each modality differently. For PDFs, I extract text while preserving layout, headings and metadata, and use OCR for scanned documents. For tables, I preserve their row-column structure instead of treating them as normal paragraphs. For images, I use OCR or a vision model to extract relevant text or descriptions, while keeping the original image when necessary. Then I index the extracted representations and maintain links back to the original source.


28. Design a RAG system for 10 million documents.

                 10M Documents
                       ↓
              Ingestion Pipeline
                       ↓
          ┌────────────┴────────────┐
          ↓                         ↓
   Text/Layout Extraction       Metadata/ACL
          ↓                         ↓
       Chunking              Permissions
          ↓                         ↓
      Embeddings ────────────────┐
          ↓                      │
   Distributed Vector DB         │
   + Hybrid/BM25 Index           │
          │                      │
          └──────────┬───────────┘
                     ↓
                  User Query
                     ↓
              Query Rewriting
                     ↓
          Hybrid + ANN Retrieval
                     ↓
               Top 50-100
                     ↓
                 Reranker
                     ↓
                  Top 5-10
                     ↓
                    LLM
                     ↓
                  Answer

For 10 million documents, I'd build a distributed asynchronous ingestion pipeline with object storage for originals and a scalable vector database for chunk embeddings and metadata. I'd use ANN indexing such as HNSW together with hybrid BM25 search, apply ACL filters during retrieval, and retrieve a larger candidate set before cross-encoder reranking. Only the top few relevant chunks would be sent to the LLM. I'd use batching, caching, sharding and replication for scalability, and evaluate the system using Recall@K, answer quality, latency, and cost
#### Your own project (expect these first)

29. ⭐ Walk me through the architecture of your RAG Q&A system.
**My RAG Q&A system has two main pipelines: ingestion and query processing. During ingestion, I extract text and structure from documents, chunk the content, generate embeddings, and store the chunks, metadata, and vectors in a vector database using an ANN index such as HNSW.**

**During query time, I authenticate the user and optionally rewrite conversational follow-up questions into standalone queries. I then perform hybrid retrieval using semantic vector search and BM25, apply metadata or access-control filters, and retrieve a relatively broad candidate set. I use a cross-encoder reranker to select the most relevant chunks, usually the top few.**

**Those chunks are passed to the LLM along with the user's question and a grounding prompt. The LLM generates the final answer based on the retrieved context, and if sufficient evidence isn't available, the system should abstain rather than hallucinate.**

**I evaluate the system separately at the retrieval and generation levels using metrics such as Recall@K, MRR, answer correctness, faithfulness, latency, and cost.**

![[rag_pipeline_architecture.png]]
29. ⭐ What were your retrieval evaluation results, and what did you change to improve them?
**Initially, our retrieval performance was around [X]% Recall@5. After analyzing the failed queries, we found that some chunks were too small and some technical queries required exact keyword matching.**

**We improved it in a few steps. First, we tuned the chunk size and overlap to preserve more context. Then we improved the embedding model based on our domain-specific queries. We also introduced hybrid retrieval, combining vector search with BM25, because semantic search alone struggled with exact terms such as API names, error codes, and technical identifiers.**

**After retrieval, we added a reranking step to improve the precision of the final context. We evaluated each change separately using Recall@K and MRR rather than changing everything at once.**

**As a result, Recall@5 improved from [X]% to [Y]%, and MRR improved from [A] to [B]. We also monitored answer correctness and faithfulness to make sure the retrieval improvement actually improved the final answers.**

30. What was the hardest failure you hit, and how did you fix it?
**The hardest issue I faced was that the RAG system sometimes gave confident but incorrect answers even though the relevant information existed in the documents.**

**At first, I thought the LLM was hallucinating, so I investigated the retrieval results instead of immediately changing the model. I found that the main problem was that the relevant information was not consistently appearing in the retrieved chunks. Some documents had complex structures, and semantic search alone wasn't always good at retrieving exact technical terms.**

**I addressed this by improving the document parsing and chunking strategy, preserving headings and metadata, and then combining vector search with keyword-based retrieval. I also added reranking so that the most relevant chunks were selected before sending them to the LLM.**

**I evaluated the changes using retrieval metrics and then checked the final answers for correctness and faithfulness. This taught me that when a RAG answer is wrong, I shouldn't immediately blame the LLM. I need to first determine whether the problem is in ingestion, retrieval, reranking, or generation.**

30. What would you do differently if you built it again?
 I would build the evaluation framework first. Then I would optimize ingestion and retrieval—especially structure-aware chunking, hybrid search, and reranking—before focusing heavily on the LLM. I would also design ACL filtering, observability, and abstention into the system from the beginning. The biggest lesson for me was that a good LLM cannot compensate for poor retrieval