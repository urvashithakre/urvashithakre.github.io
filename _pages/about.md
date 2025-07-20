---
permalink: /
title: "ProtGPT2 - A deep unsupervised language model for protein design"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Introduction
======
Proteins are essential molecules responsible for virtually all functions in living organisms. Designing new proteins could lead to breakthroughs in drug development, material science, and synthetic biology. However, this process is traditionally slow, resource-intensive, and highly specialized.

Recent progress in Transformer-based architectures has enabled the implementation of language models capable of generating text with human-like capabilities. Motivated by this success, researchers developed ProtGPT2, a language model trained on the protein space that generates de novo protein sequences following the principles of natural ones.

In this blog, we’ll explore how researchers are rethinking protein design using tools from an entirely different domain — natural language processing. We’ll look at how a language model was trained to generate protein sequences, how well those sequences mimic natural proteins, and what this could mean for the future of biology.

🔍 Whether you’re coming from biology, computer science, or just curious about AI, this post will walk you through the fascinating crossroad where deep learning meets molecular design.


From Language to Biology: A Shared Structure
======
Recent breakthroughs in Natural Language Processing (NLP) have demonstrated that large language models (LLMs) can effectively learn the structure, meaning, and composition of human language. These models are trained on vast amounts of text, enabling them to generate coherent paragraphs, translate across languages, and even write poetry.

Interestingly, proteins share a surprisingly similar structure to natural language. Proteins are linear chains of amino acids drawn from a 20-character chemical alphabet. Protein sequences, like natural languages, are information-complete: they store structure and function entirely in their amino acid order with extreme efficiency. Just like words form sentences with grammar, amino acids form structured domains that fold into functional proteins. This analogy has led to the idea of treating protein sequences as a form of language — one where sequence dictates both structure and function.

# Previous Work

Before ProtGPT2, several models laid the groundwork for applying NLP techniques to biological sequences. These efforts demonstrated that protein sequences could be understood and analyzed through the lens of language modeling — both in supervised and unsupervised settings.

🧠 Supervised Models
Many earlier models were trained on labeled data, focusing on specific prediction tasks such as:

Secondary structure prediction

Stability assessment

Homology detection

Platforms like BioSeq-BLM collected numerous supervised language models designed for biomolecular tasks.

However, supervised learning has limitations: it requires curated datasets and is narrowly focused on predefined tasks.


🔍 Unsupervised Models
The rise of Transformer architectures introduced a shift toward unsupervised learning, where models learn from raw sequences without labels. Notable models include:

| Model         | Architecture  | Focus                        |
| ------------- | ------------- | ---------------------------- |
| **ProtBERT**  | BERT-style    | Embedding proteins           |
| **TCR-BERT**  | BERT-style    | T-cell receptor modeling     |
| **ProtTrans** | BERT & T5 mix | Multi-task protein NLP       |
| **ESM**       | Transformer   | Large-scale protein modeling |


These models were typically trained using masked language modeling, where certain tokens are hidden and the model learns to reconstruct them. While effective for embedding sequences, they were not optimized for generation.

🧬 Autoregressive Models for Protein Generation
Autoregressive models — like GPT — predict the next token based on previous ones. They are naturally suited for generation tasks.

Key autoregressive protein models prior to ProtGPT2 include:

ProGen: One of the first autoregressive models to generate proteins

RITA: A family of generative Transformer models

DARK: Focused on de novo protein generation

## From Foundations to Frontier: Meet ProtGPT2
From Foundations to Frontier: Meet ProtGPT2
Building on these developments, ProtGPT2 represents a significant advancement in the application of language models to protein design.

ProtGPT2 is an autoregressive Transformer model with 738 million parameters, based on the GPT-2 architecture. It was trained on over 50 million protein sequences from the UniRef50 database using a Byte Pair Encoding (BPE) tokenizer specifically adapted for amino acid subsequences.

Unlike masked models focused on classification or embedding, ProtGPT2 was explicitly trained for sequence generation, enabling it to compose entirely new proteins that closely resemble natural ones.

✨ **Key Features of ProtGPT2**:
- Unsupervised training on massive protein sequence data  
- Generates amino acid sequences with realistic frequencies  
- Predicted to be ~88% globular, reflecting natural folding patterns  
- Explores previously unseen regions of the protein space  
- Validated with AlphaFold2, Rosetta, and molecular dynamics  




