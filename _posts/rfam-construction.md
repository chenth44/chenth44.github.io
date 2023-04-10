---  
share: true  
title: 'Construction of Rfam families'  
date: 2022-10-19  
permalink: /posts/2022/10/rfam_construction/  
tags:  
  - bioinformatics  
---  
  
This blog introduces how Rfam families are constructed.  
  
# Seed Alignment  
  
Rfam Seed alignment is a small, curated set of representative sequences for each family. The seed alignment also has a **secondary structure** annotation, which represents the **conserved** secondary structure for these sequences.  
  
A seed must satisfy that:  
- It has some known functional classification  
- It is evolutionary conserved  
- It has evidence for a secondary structure  
  
To build a new family, at least one **experimentally validated example** from the published literature is needed. Other homologues identified in the literature are added to the seed. Alternatively, if these are not available, we will try to identify other members either by similarity searching or manual curation.  
  
If no literature available, various software such as *WAR* or *RNAalifold* are used to generate an alignment and secondary structure.  
  
From the seed alignment, the *Infernal software* is used to build a **covariance model** (**CM**) for this family.  
  
# Covariance Model  
  
## Stochastic Context-Free Grammar (SCFG)  
  
Covariance model is a type of **profile stochastic context-free grammar** (**profile SCFG**).   
  
A stochastic context free grammar (SCFG) consists of the following:  
  
- $M$ different nonterminals (states), denoted by capital letters such as $V$ and $Y$  
- $K$ different terminal symbols (the observed alphabet, A, C, G, U for RNA), denoted by small letters such as $a$ and $b$  
- A number of production rules of the form $V \to \gamma$, where $\gamma$ can be any string of nonterminal and/or terminal symbols, including the empty string $\epsilon$  
- Each production rule is associated with a probability $\Pr(V \to \gamma)$, satisfying the condition $\sum_{\gamma'}\Pr(V \to \gamma') = 1$  
  
  
## Elements of a covariance model (CM)  
  
A CM is an SCFG built on an RNA secondary structure consensus. Model states of a CM are associated with base pairs and single-stranded positions. A CM has seven types of states and production rules:  
  
| **State Type** | **Description** | **Production**   | **Emission** | **Transition** |  
|:-------------- | --------------- | ---------------- | ------------ | -------------- |  
| P              | Pair Emitting   | $P \to aYb$      | $e_v(a, b)$  | $t_v(Y)$       |  
| L              | Left Emitting   | $L \to aY$       | $e_v(a)$     | $t_v(Y)$       |  
| R              | Right Emitting  | $R \to Ya$       | $e_v(a)$     | $t_v(Y)$       |  
| B              | Bifurcation     | $B \to SS$       | $1$          | $1$            |  
| D              | Delete          | $D \to Y$        | $1$          | $t_v(Y)$       |  
| S              | Start           | $S \to Y$        | $1$          | $t_v(Y)$       |  
| E              | End             | $E \to \epsilon$ | $1$          | $t_v(Y)$       |  
  
Each overall production probability is the independent product of an emission probability $e_v$ and a transition probability $t_v$, both of which are position-dependent parameters that depend on the state $v$.  
  
A CM consists of many states of these seven basic types, each with its own emission and transition probability distributions, and its own set of states that is can transition to.  
- Consensus base pairs will be modeled by P states  
- Consensus single stranded residues will be modeled by L and R states  
- Insertions relative to the consensus will be modeled by more L and R states  
- Deletions relative to consensus will be modeled by D states  
- The branching topology of the RNA secondary structure will be modeled by B, S, and E states  
  
## From Consensus Structural Alignment to Guide Tree  
  
A **guide tree** has eight types of nodes:  
  
| **State Type** | **Description**     | **Main State Type** |  
|:-------------- | ------------------- | ------------------- |  
| MATP           | Pair                | P                   |  
| MATL           | Single Strand Left  | L                   |  
| MATR           | Single Strand Right | R                   |  
| BIF            | Bifurcation         | B                   |  
| ROOT           | Root                | S                   |  
| BEGL           | Begin, Left         | S                   |  
| BEGR           | Begin, Right        | S                   |  
| END            | End                 | E                   |   
  
- The input is the secondary structure in **WUSS notation**.   
- Base pairs are assigned to MATP nodes  
- Consensus unpaired columns are assigned to MATL or MATR nodes  
- ROOT is used at the head of the tree  
- Multifurcation loops and/or multiple stems are assigned to one or more BIF nodes followed by two branches starting with BEGL and BEGR  
- Alignment columns that are considered to be insertions relative to the consensus structure are ignored  
- MATL nodes are always used instead of MATR nodes where possible  
  
## From Guide Tree to CM  
  
Each node in the master tree is expanded into one or more states in the CM as follows:  
  
| **Node** | **States**         | **Total # States** | **# of Split States** | **# of Insert States** |  
| -------- | ------------------ | ------------------ | --------------------- | ---------------------- |  
| MATP     | [MP ML MR D] IL IR | 6                  | 4                     | 2                      |  
| MATL     | [ML D] IL          | 3                  | 2                     | 1                      |  
| MATR     | [MR D] IR          | 3                  | 2                     | 1                      |  
| BIF      | [B]                | 1                  | 1                     | 0                      |  
| ROOT     | [S] IL IR          | 3                  | 1                     | 2                      |  
| BEGL     | [S]                | 1                  | 1                     | 0                      |  
| BEGR     | [S] IL             | 2                  | 1                     | 1                      |  
| END      | [E]                | 1                  | 1                     | 0                      |  
  
- "M" stands for **match** states and "I" stands for **insertion** states  
- The states are group into a **split set** (enclosed in brackets) and an **insertion set**  
- Each state in the split set has a possible transition to every insert state in the same node, and to every state in the split set of the next node  
- An IL state makes a transition to itself, to the IR state in the same node (if present), and to every state in the split set of the next node  
- An IR state makes a transition to itself and to every state in the split set of the next node  
- Insertion states that are immediately before an END node are effectively detached from the model by making transitions into them impossible.  
  
> In other words, a node starts with split state, followed by arbitrary number of insertion states; it then makes a transition to a split state of the next node.  
  
The algorithm will find a maximum likelihood parse tree for a given sequence. (i.e. Find a parse tree maximizing the probability of the given sequence generated by this parse tree.)  
  
## Parameterization  
  
Using the guide tree and the final CM, each individual sequence in the input multiple alignment can be converted unambiguously to a CM parse tree. Weighted counts for observed state transitions and singlet/pair emissions are then collected form these parse trees. These counts are converted to transition and emission probabilities, as maximum a posteriori estimates using mixture Dirichlet priors.  
  
# Using Infernal to Generate and Search a CM  
  
### `cmbuild` for Building a CM  
  
`cmbuild <cm_file> <input_file>` will take the multiple sequence alignment of an RNA family in **Stockholm format** as the input, and then output a covariance model for this family.  
  
  
### `cmcalibrate` for Fitting Exponential Tails For Covariance Model E-Value Determination  
  
`cmcalibrate <cm_file>` generates random sequences and searches them with the CM. A histogram of the bit scores of the resulting hits is fit to an exponential tail, and the parameters of the fitted tail are saved to the CM file. The exponential tail parameters are then used to estimate the statistical significance of hits found in `cmsearch` and `cmscan`.  
  
### `cmsearch` for Searching CMs Against a Sequence Database  
  
`cmsearch <cm_file> <seq_file>` searches CMs for each sequence in `<seq_file>`. Given a CM and a sequence, Infernal will first calculate the bit score of the sequence w.r.t. the CM:  
$$  
\text{bit score} \triangleq \log_2 \frac{\Pr(D \mid M)}{\Pr(D \mid N)}\ ,  
$$  
where $D$ stands for the sequence, $M$ stands for the CM, and $N$ stands for the null model, which is a single state equiprobable A, C, G and U.  
  
Given the current bit score $x$ and the bit score calculated in `cmcalibrate`, we can compute the E-value, which is the number of hits expected for a random sequence input to reach the bit score of at least $x$. The lower E-value, the more significant the hit.  
  
# Building Rfam Using Infernal  
  
1. Build a CM for each family using the seed alignment  
2. For each non-seed sequence in the *rfamseq* database, find the most significant hit to a CM. If the score is significant enough (the bit score is above a **gathering threshold**), add the sequence to the family.  
3. If the sequence is believed to improve the seed, the sequence is also added to the seed. Repeat 1 to regenerate the CM.  
  
# Expanding Rfam Using Infernal + CNN (Proposed)  
  
1. Use Infernal to search *rfamseq*, and find matches with bit score below each family gathering threshold  
2. Use all Pfam sequences to train the CNN and use the trained model to predict family membership for each insignificant match  
3. Select sequences with high CNN and Infernal confidence  
4. Select sequences that the CNN and Infernal agree at the family level  
5. Filter for duplicate sequences & sequences overlap with Rfam  
6. Add these sequences to Rfam