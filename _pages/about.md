---
permalink: /
title: "ProtGPT2 - A deep unsupervised language model for protein design"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---
<style>
p {
    text-align: justify;
}
</style>
## Introduction
 Proteins are essential molecules responsible for virtually all functions in living organisms. Designing new proteins could lead to breakthroughs in drug development, material science, and synthetic biology. However, this process is traditionally slow, resource-intensive, and highly specialized.

Recent progress in Transformer-based architectures has enabled the implementation of language models capable of generating text with human-like capabilities. Motivated by this success, researchers developed ProtGPT2, a language model trained on the protein space that generates de novo protein sequences following the principles of natural ones.

In this blog, we’ll explore how researchers are rethinking protein design using tools from an entirely different domain — natural language processing. We’ll look at how a language model was trained to generate protein sequences, how well those sequences mimic natural proteins, and what this could mean for the future of biology.

🔍 Whether you’re coming from biology, computer science, or just curious about AI, this post will walk you through the fascinating crossroad where deep learning meets molecular design.

## From Language to Biology: A Shared Structure
Recent breakthroughs in Natural Language Processing (NLP) have demonstrated that large language models (LLMs) can effectively learn the structure, meaning, and composition of human language. These models are trained on vast amounts of text, enabling them to generate coherent paragraphs, translate across languages, and even write poetry.

Interestingly, proteins share a surprisingly similar structure to natural language. Proteins are linear chains of amino acids drawn from a 20-character chemical alphabet. Protein sequences, like natural languages, are information-complete: they store structure and function entirely in their amino acid order with extreme efficiency. Just like words form sentences with grammar, amino acids form structured domains that fold into functional proteins. This analogy has led to the idea of treating protein sequences as a form of language — one where sequence dictates both structure and function.

## Previous Work
Before ProtGPT2, several models laid the groundwork for applying NLP techniques to biological sequences. These efforts demonstrated that protein sequences could be understood and analyzed through the lens of language modeling — both in supervised and unsupervised settings.

### 🧠 Supervised Models
Many earlier models were trained on labeled data, focusing on specific prediction tasks such as:
- Secondary structure prediction
- Stability assessment
- Homology detection

Platforms like BioSeq-BLM collected numerous supervised language models designed for biomolecular tasks. However, supervised learning has limitations: it requires curated datasets and is narrowly focused on predefined tasks.

### 🔍 Unsupervised Models
The rise of Transformer architectures introduced a shift toward unsupervised learning, where models learn from raw sequences without labels. Notable models include:

| Model         | Architecture  | Focus                        |
| ------------- | ------------- | ---------------------------- |
| **ProtBERT**  | BERT-style    | Embedding proteins           |
| **TCR-BERT**  | BERT-style    | T-cell receptor modeling     |
| **ProtTrans** | BERT & T5 mix | Multi-task protein NLP       |
| **ESM**       | Transformer   | Large-scale protein modeling |


These models were typically trained using masked language modeling, where certain tokens are hidden and the model learns to reconstruct them. While effective for embedding sequences, they were not optimized for generation.
</p>

### 🧬 Autoregressive Models for Protein Generation
Autoregressive models — like GPT — predict the next token based on previous ones. They are naturally suited for generation tasks.

Key autoregressive protein models prior to ProtGPT2 include:

- ProGen: One of the first autoregressive models to generate proteins
- RITA: A family of generative Transformer models
- DARK: Focused on de novo protein generation

## From Foundations to Frontier: Meet ProtGPT2
Building on recent advances in language modeling, ProtGPT2 represents a powerful leap in applying deep learning to protein design. ProtGPT2 is an autoregressive Transformer model with 738 million parameters, based on the GPT-2 architecture. That means it generates outputs sequentially, one token at a time, conditioned only on what came before - perfect for modeling protein sequences. 

Given a protein sequence, $W = \{ w_1, w_2, \dots, w_n \}$, the model learns to predict the probability of each amino acid conditioned on its preceding tokens:

$$
p(W) = \prod_{i=1}^{n} p(w_i \mid w_{<i})
$$

The training process minimizes the **negative log-likelihood** over all protein sequences in the dataset:

$$
L_{\text{CLM}} = - \sum_{k=1}^{|D|} \sum_{i=1}^{|w_k|} \log\, p_\theta(w_{k,i} \mid w_{k,<i})
$$

Where:

- $w_{k,i}$: *i*-th amino acid in the *k*-th protein sequence  
- $D$: protein dataset (UniRef50)  
- $\theta$: model parameters  
- $L_{\text{CLM}}$: Causal Language Modeling loss

This formulation allows ProtGPT2 to learn complex statistical dependencies — such as conserved motifs and structural sub-patterns — directly from sequence data.

## The Dataset: UniRef50
ProtGPT2 was trained on UniRef50 (version 2021_04) — a clustered version of UniProt with 50% sequence identity, which ensures a balance between diversity and redundancy reduction.

| Subset       | Sequences           |
| ------------ | ------------------- |
| Training Set | \~44.9 million      |
| Validation   | \~4.9 million (10%) |

This dataset spans both known and “dark” proteome regions — sequences with no known structure or function — enabling the model to generalize across a vast protein landscape.
</p>

## Byte Pair Encoding (BPE) for Tokenization
<p align="justify"> Rather than treating each amino acid as a separate token, ProtGPT2 uses a Byte Pair Encoding (BPE) tokenizer — a subword algorithm that learns common amino acid motifs and folds them into reusable building blocks.

- Vocabulary size: 50,256 tokens
- Average token: ~4 amino acids
- Trained on: Swiss-Prot subset for robustness

This strategy reduces sequence length, improves generalization, and helps the model learn biologically meaningful patterns.

### ⚙️ Final Model Configuration
| Component   | Description                        |
|-------------|------------------------------------|
| Architecture | GPT-2 large (decoder-only)        |
| Layers       | 36                                 |
| Parameters   | 738 million                        |
| Batch Size   | 65,536 tokens per batch            |
| Optimizer    | Adam (β₁ = 0.9, β₂ = 0.999)        |
| Hardware     | 128 NVIDIA A100 GPUs for 4 days    |

Unlike masked models focused on classification or embedding, ProtGPT2 was explicitly trained for sequence generation, enabling it to compose entirely new proteins that closely resemble natural ones. To summarize, ProtGPT2 combines a powerful GPT-2 architecture with a massive protein sequence corpus (UniRef50) and a subword-aware BPE tokenizer. Together, these components enable the model to learn the underlying "language" of proteins and generate new sequences that reflect natural structural and functional properties.

![Fig. 1: ProtGPT2 Architecture](images/ProtGPT_Architecture.png)

## Sequence Generation Strategies
Once ProtGPT2 is trained to model the protein language, the next step is generating new sequences. But how exactly are these sequences "sampled" from the model?

Once we’ve trained our model, we need to decide how to generate sequences from it. The model gives us a probability distribution over possible amino acids at each
step—but how we sample from that distribution dramatically affects the quality of the output.
| Strategy           | Description                                                           | Outcome                              |
|--------------------|-----------------------------------------------------------------------|--------------------------------------|
| **Greedy**         | Always selects the most probable amino acid at each step              | Repetitive, low-diversity sequences  |
| **Beam Search**    | Maintains multiple candidate sequences and picks the best-scoring one | Slightly better but still repetitive |
| **Random (Top-k)** | Samples from top-k probable tokens randomly                           | Diverse and biologically realistic   |


Best Strategy?
The authors found that Top-k sampling (k = 950) combined with a repetition penalty of 1.2 yields the best results.
This approach balances diversity and realism, producing amino acid propensities close to those found in natural proteins.
Here’s a visual representation of how decoding strategies affect output:

![Fig. 2: Sampling outputs for GPT2-like language models on both text (a–d) and protein sequences (e–h). Repetitive sequences are generated by greedy and beam search; natural-like diversity emerges with random top-k sampling (g, h).](images/Sampling Output.jpg)




