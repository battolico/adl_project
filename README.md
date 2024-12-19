I am still conducting the project and I will finish it during the winter break as I was busy.

I am conducting an ablation study on a GPT Transformer. I will start by systematically modifying key configurations of the model to analyze their impact on performance while keeping the total number of parameters approximately fixed. 
Specifically, I will explore by varying the following configurations:
- Embedding Dimension
- Number of Attention Heads
- Feed-Forward Neural Network

The total number of parameters in the GPT Transformer I will use, can be calculed like this:

- **Calculating the Embedding Layer Parameters**
    - Embedding Parameters
        - Vocab_size * Embedding_dim
    - Positional Encoding / Embedding
        - Positional Embeddings are added to the token embeddings
        - Sequence Length = L = context window
        - L * Embedding_dim
    - Total Embedding Layer Parameters
    - Embedding_Parameters + Positional_Encoding = (***V* x *E*)+(*L* x *E*)**
- **Transformer Layer Parameters**
    - Multi-Head Attention
        - Each attention has 3 sets of weights: Q, K, V
        - Each head has a dimension E_h = E / h(#of heads)
            - d_head = d_model // n_head
        - # of params for Q, K, V in a single head is: 3 * (E_h * E)
            - becomes 3*E^2
            - Add a bias vector of 3*E
        - Output of MHA is a weighted sum of the heads —> requires an additional projection, with weights **Wo: E*E**
            - **Wo** is the **output projection matrix**
            - The output of all $h$ are concatenated into a single matrix of size $T * E$  → which is projected back to the model dimension E → using $W_o$ → size $E^2$
                - additional bias vector of size $E$
        - Total parameters for multi-head attention: *3E² + 3E + E² + E= **4E²+4E*.**
    - **FF NN or MLP**
        - responsible for providing most of the computation of the transformer
            - typically comprised for four sublayers: c_fc, c_proj, act, dropout
        - c_fc is reponsible for projecting the output of the Attention layer into a hidden space - **Hidden space (H)**, where H=4E
        - The c_fc is of size E by H plus a bias vector of size H
            - Therefore, c_fc = E*H + H
        - c_proj is responsible for projecting the output of the first feed-forward layer back into the embedding space
            - c_proj = H*E + E
        - total params for FFNN: (E*H + H) + (H*E + E) = **2*EH + E + H**
    - **Layer Normalisation**
        - $\gamma$ and $\beta$ are learnable parameters of size $E$, —> 2E
        - we have 2 Layer Norms
        - **4*E**
    - Final Layer Norm
    - TOTAL
        - **EmbeddingLayer + Layers *(MultiheadAttention + FeedForwardLayer + LayerNormalization) + FinalLayerNormalization**
        - **VE + LE + L(4E²+4E + 2EH + E + H + 4E) + 2E**

By varying one parameter at a time while keeping others balanced using the above equation. 
As a metric I will use perplexity to asses how the model performance changes as the parametersallocation in the model changes.
