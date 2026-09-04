<a id="top"></a>

# Time-Series Memory

> A curated, continuously updated collection of research on **memory mechanisms for time-series modeling** — the companion paper collection for the survey **_Memory in Deep Time-Series Models_** (Nguyen, Nguyen, Nguyen, Do, Nguyen & Le; Deakin Applied AI Initiative, A2I2).

Deep learning for time series has moved through recurrence, attention, structured state, retrieval augmentation, foundation models, and tool-using agents. These are usually surveyed apart. This collection organizes them around a single question:

> **How does a time-series model retain and access information beyond its immediate input window?**

We call the answer a model's **memory**, and we place every method on one spectrum — from *implicit internal state* to *explicitly curated, agent-managed stores*.

<p align="center">
  <b><a href="#internal">🧠 Internal</a> &nbsp;→&nbsp; <a href="#explicit">💾 Explicit</a> &nbsp;→&nbsp; <a href="#retrieval">🔎 Retrieval</a> &nbsp;→&nbsp; <a href="#agentic">🤖 Agentic</a></b><br>
  <sub>implicit ─────────────────────────────────────────► actively curated</sub>
</p>

---

<a id="toc"></a>
## 📑 Table of Contents

| | Section | What's inside |
|---|---|---|
| 🗺️ | **[Taxonomy at a Glance](#taxonomy)** | The four memory classes and their subclasses |
| 📐 | **[Four Axes of Memory](#axes)** | Representation · Capacity · Persistence · Access |
| 🧠 | **[Internal Memory](#internal)** | [Recurrent State](#internal-recurrent) · [Structured State](#internal-structured) |
| 💾 | **[Explicit Memory](#explicit)** | [Verbatim](#explicit-verbatim) · [Slot](#explicit-slot) · [Learned](#explicit-learned) |
| 🔎 | **[Retrieval Memory](#retrieval)** | [Instance](#retrieval-instance) · [Latent](#retrieval-latent) · [Knowledge](#retrieval-knowledge) |
| 🤖 | **[Agentic Memory](#agentic)** | [Episodic & Working](#agentic-episodic) · [Semantic](#agentic-semantic) · [Procedural](#agentic-procedural) |
| 🧪 | **[Benchmarks & Resources](#benchmarks)** | Evaluation families, memory-specific benchmarks, libraries |
| 📚 | **[Related Surveys](#surveys)** | Positioning against prior surveys |
| 🤝 | **[Contributing](#contributing)** · **[Citation](#citation)** | How to add a paper |

---

<a id="taxonomy"></a>
## 🗺️ Taxonomy at a Glance

We organize the field into four architectural classes. The distinction is **where historical information is stored and how the model interacts with it**.

| | Class | Core question | Where memory lives | Capacity | Persistence |
|:---:|---|---|---|---|---|
| 🧠 | **[Internal](#internal)** | *Can the past be compressed into state?* | Weights & fixed-size hidden state | Architecturally bounded | Per-sequence |
| 💾 | **[Explicit](#explicit)** | *Can specific pieces of the past stay addressable?* | Individually addressable slots/entries inside the model | Architecturally bounded | Per-sequence / per-dataset |
| 🔎 | **[Retrieval](#retrieval)** | *Which records should be recalled for this query?* | External store searched at inference | Data-scaled (unbounded) | Per-dataset |
| 🤖 | **[Agentic](#agentic)** | *What should be written, revised, and forgotten?* | External store governed by a controller | Policy-bounded | Online (in principle) |

**Subclasses**

- 🧠 **Internal** → [Recurrent state](#internal-recurrent) (RNN/LSTM/GRU) · [Structured state](#internal-structured) (S4/Mamba)
- 💾 **Explicit** → [Verbatim](#explicit-verbatim) (attention context) · [Slot](#explicit-slot) (NTM/DNC-style banks) · [Learned](#explicit-learned) (prototypes, pattern banks)
- 🔎 **Retrieval** → [Instance](#retrieval-instance) (past windows/trajectories) · [Latent](#retrieval-latent) (embeddings, tokens) · [Knowledge](#retrieval-knowledge) (text, events, KGs)
- 🤖 **Agentic** → [Episodic & working](#agentic-episodic) · [Semantic](#agentic-semantic) · [Procedural](#agentic-procedural)

<sub>[⬆ back to top](#top)</sub>

---

<a id="axes"></a>
## 📐 Four Axes of Memory

Every method in this collection can be described along four axes, independent of its backbone.

| Axis | Question | Values |
|---|---|---|
| **Representation** | What form does retained information take? | Implicit fixed-dim state · explicit vector entries · retained raw observations |
| **Capacity** | How does memory scale with the data horizon? | **Architecturally bounded** (fixed limit) · **Data-scaled** (grows with data) · **Policy-bounded** (limited by eviction rules or budgets) |
| **Persistence** | How long does an entry survive? | **Per-sequence** (resets) · **Per-dataset** (built once, frozen at deployment) · **Online** (evolves during deployment) |
| **Access** | How is memory written and read? | Write: recurrent update, gated update, append, overwrite, controller-issued · Read: dense attention, similarity search, top-*k* nearest neighbour, learned controller |

> Capacity and persistence are **independent**: a store can be bounded yet persist through deployment, or data-scaled yet aggressively evict.

<sub>[⬆ back to top](#top)</sub>

---

<a id="internal"></a>
## 🧠 Internal Memory

> Past information is kept **inside the model**, compressed into a bounded hidden state.

<a id="internal-recurrent"></a>
### 🔄 Recurrent State

A hidden state is carried forward and updated at every step.

| Year | Method | Venue | What the memory holds |
|---|---|---|---|
| 1990 | [**RNN** — Finding Structure in Time](https://scholar.google.com/scholar?q=%22Finding%20structure%20in%20time%22) | Cognitive Science | Establishes the evolving hidden state as a summary of prior observations. |
| 1997 | [**LSTM** — Long Short-Term Memory](https://scholar.google.com/scholar?q=%22Long%20short-term%20memory%22) | Neural Computation | Gated cell state controls what is retained, discarded, and exposed per step. |
| 2014 | [**GRU** — RNN Encoder–Decoder for SMT](https://scholar.google.com/scholar?q=%22Learning%20phrase%20representations%20using%20RNN%20encoder-decoder%20for%20statistical%20machine%20translation%22) | EMNLP | Simplified gating that merges update and reset of the recurrent state. |
| 2015 | [**LSTM-AD** — LSTM Networks for Anomaly Detection](https://api.semanticscholar.org/CorpusID:43680425) | ESANN | Recurrent state as a compact model of *expected* behaviour; deviations flag anomalies. |
| 2016 | [**EncDec-AD** — LSTM Encoder–Decoder for Multi-Sensor AD](https://arxiv.org/abs/1607.00148) | arXiv | Encoder–decoder recurrent states model normal multi-sensor dynamics. |
| 2017 | [**DA-RNN** — Dual-Stage Attention-Based RNN](https://scholar.google.com/scholar?q=%22A%20dual-stage%20attention-based%20recurrent%20neural%20network%20for%20time%20series%20prediction%22) | IJCAI | Dual-stage attention lets recurrent states select informative variables and time steps. |
| 2017 | [**TreNet** — Hybrid Neural Networks for Trend](https://doi.org/10.24963/ijcai.2017/316) | IJCAI | CNN local features feed LSTM representations of longer-term trend. |
| 2018 | [**LSTNet** — Long- and Short-Term Temporal Patterns](https://scholar.google.com/scholar?q=%22Modeling%20long-and%20short-term%20temporal%20patterns%20with%20deep%20neural%20networks%22) | SIGIR | Conv-recurrent hybrid + skip-RNN for periodicity + autoregressive component. |
| 2018 | [**GRU-D** — RNNs for MTS with Missing Values](https://scholar.google.com/scholar?q=%22Recurrent%20neural%20networks%20for%20multivariate%20time%20series%20with%20missing%20values%22) | Scientific Reports | Trainable exponential decay in the cell update encodes missingness intervals. |
| 2018 | [**BRITS** — Bidirectional Recurrent Imputation](https://scholar.google.com/scholar?q=%22BRITS%3A%20bidirectional%20recurrent%20imputation%20for%20time%20series%22) | NeurIPS | Twin bidirectional recurrent states iteratively impute missing values. |
| 2018 | [**SAnD** — Attend and Diagnose](https://scholar.google.com/scholar?q=%22Attend%20and%20diagnose%3A%20Clinical%20time%20series%20analysis%20using%20attention%20models%22) | AAAI | Self-attention layered over recurrent clinical representations. |
| 2019 | [**LSTM-FCN** — Multivariate LSTM-FCNs](https://scholar.google.com/scholar?q=%22Multivariate%20LSTM-FCNs%20for%20time%20series%20classification%22) | Neural Networks | Convolutional + recurrent pathways encode discriminative temporal patterns. |
| 2020 | [**DeepAR** — Probabilistic Forecasting with AR RNNs](https://scholar.google.com/scholar?q=%22DeepAR%3A%20Probabilistic%20forecasting%20with%20autoregressive%20recurrent%20networks%22) | Int. J. Forecasting | Autoregressive recurrent state shared across many related series. |
| 2021 | [**CloudLSTM** — Spatiotemporal Point-Cloud Streams](https://scholar.google.com/scholar?q=%22Cloudlstm%3A%20A%20recurrent%20neural%20model%20for%20spatiotemporal%20point-cloud%20stream%20forecasting%22) | AAAI | Dynamic Point-cloud Convolution inside the cell encodes spatial *and* temporal structure. |
| 2024 | [**LSTM-DRL** — Cascaded LSTM for Automated Trading](https://scholar.google.com/scholar?q=%22A%20novel%20deep%20reinforcement%20learning%20based%20automated%20stock%20trading%20system%20using%20cascaded%20lstm%20networks%22) | Expert Systems with Applications | Recurrent state as the memory of a sequential decision-making policy. |
| 2025 | [**P-sLSTM** — Unlocking the Power of LSTM](https://scholar.google.com/scholar?q=%22Unlocking%20the%20power%20of%20lstm%20for%20long%20term%20time%20series%20forecasting%22) | AAAI | Shows recurrent memory remains competitive for modern long-term forecasting. |

<a id="internal-structured"></a>
### 🌀 Structured State

Hand-designed gates are replaced by parameterized linear state dynamics.

| Year | Method | Venue | What the memory holds |
|---|---|---|---|
| 2022 | [**S4** — Efficiently Modeling Long Sequences with Structured State Spaces](https://scholar.google.com/scholar?q=%22Efficiently%20modeling%20long%20sequences%20with%20structured%20state%20spaces%22) | ICLR | Structured state transitions give efficient long-range compression. |
| 2022 | [**FiLM** — Frequency-Improved Legendre Memory Model](https://scholar.google.com/scholar?q=%22FiLM%3A%20Frequency%20improved%20Legendre%20memory%20model%20for%20long-term%20time%20series%20forecasting%22) | NeurIPS | Legendre projections + frequency-domain structure enlarge effective state capacity. |
| 2023 | [**Mamba** — Linear-Time Sequence Modeling with Selective State Spaces](https://arxiv.org/abs/2312.00752) | arXiv / COLM | Input-dependent transitions selectively retain or discard, in linear time. |
| 2024 | [**TimeMachine** — A Time Series is Worth 4 Mambas](https://arxiv.org/abs/2403.09898) | arXiv | Mamba-based components for long-term forecasting. |
| 2024 | [**MambaTS** — Improved Selective State Space Models](https://arxiv.org/abs/2405.16440) | arXiv | Adapts selective state-space modeling to long-term temporal dependence. |
| 2024 | [**Bi-Mamba+** — Bidirectional Mamba](https://arxiv.org/abs/2404.15772) | arXiv | Bidirectional processing to integrate features over longer ranges. |
| 2024 | [**SST** — Multi-Scale Hybrid Mamba–Transformer Experts](https://arxiv.org/abs/2404.14757) | arXiv | Combines compressive state with attention-based context across scales. |
| 2025 | [**MAAT** — Mamba Adaptive Anomaly Transformer](https://scholar.google.com/scholar?q=%22Mamba%20Adaptive%20Anomaly%20Transformer%20with%20association%20discrepancy%20for%20time%20series%22) | Eng. Appl. of AI | Mamba + sparse attention for association discrepancy in anomaly detection. |
| 2025 | [**S-Mamba** — Is Mamba Effective for Time Series Forecasting?](https://scholar.google.com/scholar?q=%22Is%20Mamba%20effective%20for%20time%20series%20forecasting%3F%22) | Neurocomputing | Empirical study: selective state compression is *not* universally optimal. |
| 2026 | [**MambaSL** — Single-Layer Mamba for Classification](https://arxiv.org/abs/2604.15174) | arXiv | Efficient single-layer selective state for discriminative tasks. |
| 2026 | [**ms-Mamba** — Multi-Scale Mamba](https://scholar.google.com/scholar?q=%22ms-mamba%3A%20Multi-scale%20mamba%20for%20time-series%20forecasting%22) | Neurocomputing | Varying sampling rates capture multi-scale temporal context. |

> **Coverage & limits.** Better state dynamics make compression more efficient and adaptive, but they never make individual historical experiences *persistent and addressable*. A model may learn that an important event occurred, yet cannot retrieve it as a record or selectively revise it. That boundary motivates [explicit memory](#explicit).

<sub>[⬆ back to top](#top)</sub>

---

<a id="explicit"></a>
## 💾 Explicit Memory

> Information is kept in a store of **individually addressable entries**, read by content-based attention.

<a id="explicit-verbatim"></a>
### 📜 Verbatim Memory

Every observed timestep (or patch) gets its own entry. Nothing is merged or summarized; forgetting is truncation only. The limiting case is the Transformer attention context.

| Year | Method | Venue | What the memory holds |
|---|---|---|---|
| 2017 | [**Transformer** — Attention Is All You Need](https://scholar.google.com/scholar?q=%22Attention%20is%20all%20you%20need%22) | NeurIPS | The input window *is* the store; every position addressable until it scrolls out. |
| 2019 | [**LogTrans** — Breaking the Memory Bottleneck](https://scholar.google.com/scholar?q=%22Enhancing%20the%20Locality%20and%20Breaking%20the%20Memory%20Bottleneck%20of%20Transformer%20on%20Time%20Series%20Forecasting%22) | NeurIPS | Explicitly framed as breaking self-attention's memory bottleneck on long series. |
| 2021 | [**Informer** — Beyond Efficient Transformer](https://scholar.google.com/scholar?q=%22Informer%3A%20Beyond%20efficient%20transformer%20for%20long%20sequence%20time-series%20forecasting%22) | AAAI | Sparse reads over an intact per-position store. |
| 2021 | [**Autoformer** — Decomposition Transformers with Auto-Correlation](https://scholar.google.com/scholar?q=%22Autoformer%3A%20Decomposition%20transformers%20with%20auto-correlation%20for%20long-term%20series%20forecasting%22) | NeurIPS | Restructured read (auto-correlation) rather than a changed store. |
| 2021 | [**Anomaly Transformer** — Association Discrepancy](https://scholar.google.com/scholar?q=%22Anomaly%20transformer%3A%20Time%20series%20anomaly%20detection%20with%20association%20discrepancy%22) | ICLR | Attention over the window scored by prior-vs-series association discrepancy. |
| 2022 | [**FEDformer** — Frequency Enhanced Decomposed Transformer](https://scholar.google.com/scholar?q=%22Fedformer%3A%20Frequency%20enhanced%20decomposed%20transformer%20for%20long-term%20series%20forecasting%22) | ICML | Frequency-domain read that thins cost, entries untouched. |
| 2023 | [**PatchTST** — A Time Series is Worth 64 Words](https://scholar.google.com/scholar?q=%22A%20time%20series%20is%20worth%2064%20words%3A%20Long-term%20forecasting%20with%20transformers%22) | ICLR | Coarsens the unit of storage from timestep to **patch** — now the default. |
| 2024 | [**TimesFM** — A Decoder-Only Foundation Model](https://scholar.google.com/scholar?q=%22A%20decoder-only%20foundation%20model%20for%20time-series%20forecasting%22) | ICML | One key-value entry per patch in a decoder-only cache. |
| 2024 | [**Timer** — Generative Pre-trained Transformers as Large TS Models](https://scholar.google.com/scholar?q=%22Timer%3A%20Generative%20Pre-trained%20Transformers%20Are%20Large%20Time%20Series%20Models%22) | ICML | Patch-level verbatim cache at foundation-model scale. |
| 2024 | [**Chronos** — Learning the Language of Time Series](https://openreview.net/group?id=TMLR) | TMLR | Quantizes values into a discrete vocabulary; tokens attended individually. |
| 2024 | [**Moirai** — Unified Training of Universal Forecasting Transformers](https://scholar.google.com/scholar?q=%22Unified%20Training%20of%20Universal%20Time%20Series%20Forecasting%20Transformers%22) | ICML | Flattened patch sequences inside a masked encoder. |
| 2024 | [**MOMENT** — A Family of Open TS Foundation Models](https://scholar.google.com/scholar?q=%22MOMENT%3A%20A%20family%20of%20open%20time-series%20foundation%20models%22) | ICML | Masked-encoder patch store across tasks. |
| 2024 | [**iTransformer** — Inverted Transformers](https://scholar.google.com/scholar?q=%22iTransformer%3A%20Inverted%20transformers%20are%20effective%20for%20time%20series%20forecasting%22) | ICLR | **Boundary case:** verbatim *across variates*, consolidated *over time*. |
| 2024 | [**Time-LLM** — Reprogramming Large Language Models](https://scholar.google.com/scholar?q=%22Time-LLM%3A%20Time%20Series%20Forecasting%20by%20Reprogramming%20Large%20Language%20Models%22) | ICLR | Reprograms patch embeddings into a frozen LM's token space. |
| 2025 | [**ChatTS** — Aligning Time Series with LLMs](https://doi.org/10.14778/3742728.3742735) | VLDB | Interleaves TS tokens with text: one attention context as a joint store. |
| 2025 | [**ITFormer** — Bridging Time Series and Natural Language](https://scholar.google.com/scholar?q=%22ITFormer%3A%20Bridging%20Time%20Series%20and%20Natural%20Language%20for%20Multi-Modal%20QA%20with%20Large-Scale%20Multitask%20Dataset%22) | ICML | Multi-modal QA over a shared time-series/text context. |

> The store grows linearly with context length `L` and attention over it grows quadratically — which is why most of this literature works on making the **read** cheaper rather than changing what the store holds.

<a id="explicit-slot"></a>
### 🗄️ Slot Memory

A fixed set of locations holding *model states* rather than observations, with learned read/write addressing.

| Year | Method | Venue | What the memory holds |
|---|---|---|---|
| 2014 | [**NTM** — Neural Turing Machines](https://arxiv.org/abs/1410.5401) | arXiv | Controller + addressable matrix; the addressing itself is learned. |
| 2016 | [**DNC** — Hybrid Computing with Dynamic External Memory](https://scholar.google.com/scholar?q=%22Hybrid%20computing%20using%20a%20neural%20network%20with%20dynamic%20external%20memory%22) | Nature | Adds dynamic allocation and temporal link tracking to the slot matrix. |
| 2018 | [**VMED** — Variational Memory Encoder–Decoder](https://proceedings.neurips.cc/paper_files/paper/2018/file/e57c6b956a6521b28495f2886ca0977a-Paper.pdf) | NeurIPS | Couples the store with a latent variable so reads inject stochastic structure. |
| 2019 | [**MemAE** — Memorizing Normality to Detect Anomaly](https://scholar.google.com/scholar?q=%22Memorizing%20normality%20to%20detect%20anomaly%3A%20Memory-augmented%20deep%20autoencoder%20for%20unsupervised%20anomaly%20detection%22) | ICCV | Decoder restricted to a sparse mixture of memorized *normal* prototypes. |
| 2020 | [**MNAD** — Learning Memory-Guided Normality](https://scholar.google.com/scholar?q=%22Learning%20Memory-Guided%20Normality%20for%20Anomaly%20Detection%22) | CVPR | Input-dependent but **ungated** write; stability from compactness/separateness losses. |
| 2022 | [**PM-MemNet** — Pattern Matching Memory Networks](https://openreview.net/forum?id=wwDg3bbYBIq) | ICLR | Clustered traffic patterns as memory keys; forecasting recast as pattern matching. |
| 2023 | [**MegaCRN** — Spatio-Temporal Meta-Graph Learning](https://doi.org/10.1609/aaai.v37i7.25976) | AAAI | Meta-Node Bank whose retrieved slots **generate the graph** itself. |
| 2024 | [**STanHop** — Sparse Tandem Hopfield Model](https://scholar.google.com/scholar?q=%22STanHop%3A%20Sparse%20Tandem%20Hopfield%20Model%20for%20Memory-Enhanced%20Time%20Series%20Prediction%22) | ICLR | Modern Hopfield layers — the only reviewed method that **quantifies** retrieval capacity and error. |
| 2025 | [**Titans** — Learning to Memorize at Test Time](https://arxiv.org/abs/2501.00663) | arXiv | **Boundary case:** learns its *write rule* at test time, but the store is parametric fast-weights, not an addressable slot matrix. |

<a id="explicit-learned"></a>
### 🧬 Learned Memory

Compact learned representations — prototypes, centroids, shared pattern banks. The dominant explicit form in time series: keep a few entries that summarize recurring structure and explain a query as a mixture of them.

| Year | Method | Venue | Task | What the memory holds |
|---|---|---|---|---|
| 2018 | [**MTNet** — Memory-Network for Multivariate Forecasting](https://arxiv.org/abs/1809.02105) | arXiv | Forecasting | Early memory-network formulation for multivariate forecasting. |
| 2020 | [**TapNet** — Attentional Prototypical Network](https://api.semanticscholar.org/CorpusID:210703726) | AAAI | Classification | Class prototypes learned jointly with the embedding — the bank *is* the classifier. |
| 2020 | [**DPSN** — Interpretable Few-Shot Classification](https://scholar.google.com/scholar?q=%22Interpretable%20time-series%20classification%20on%20few-shot%20samples%22) | IJCNN | Classification | Discriminative subsequences as interpretable stored representatives. |
| 2021 | [**ShapeNet** — Shapelet-Neural Network](https://doi.org/10.1609/aaai.v35i9.17018) | AAAI | Classification | Shapelet store embedded in a neural classifier. |
| 2022 | [**MAGL** — Memory Augmented Graph Learning](https://scholar.google.com/scholar?q=%22Memory%20augmented%20graph%20learning%20networks%20for%20multivariate%20time%20series%20forecasting%22) | CIKM | Forecasting | Global historical features recorded to mine spatial correlations. |
| 2023 | [**MEMTO** — Memory-Guided Transformer](https://scholar.google.com/scholar?q=%22MEMTO%3A%20Memory-guided%20Transformer%20for%20Multivariate%20Time%20Series%20Anomaly%20Detection%22) | NeurIPS | Anomaly det. | **Learned gate** decides how much each entry absorbs; *k*-means init stabilizes a two-phase update. |
| 2024 | [**Memory Shapelet Learning** — Early Classification of Streaming Series](https://doi.org/10.1109/TCYB.2023.3337550) | IEEE T-Cybernetics | Classification | Shapelet store maintained **online** during deployment. |
| 2025 | [**HAMN** — Memory Augmented Coherent Probabilistic Forecasts](https://doi.org/10.1016/j.neucom.2025.131075) | Neurocomputing | Forecasting | Store organized by aggregation level so sparse series draw on their aggregates. |
| 2025 | [**MOMEMTO** — Patch-Based Memory Gate](https://arxiv.org/abs/2509.18751) | arXiv | Anomaly det. | Inherits the MEMTO gate under a patch-based foundation model. |
| 2025 | [**MemMambaAD** — Memory-Augmented State Space Model](https://api.semanticscholar.org/CorpusID:279357027) | Eng. Appl. of AI | Anomaly det. | Gated bank paired with a state-space encoder. |
| 2025 | [**MMNet** — Missing-Aware and Memory-Enhanced Network](https://doi.org/10.24963/ijcai.2025/357) | IJCAI | Imputation | Reconstructs from *global* dataset similarity, not just local context. |
| 2025 | [**PRIME** — Imputation with Inter-Series Prototypes](https://doi.org/10.1007/s11390-025-4333-3) | J. Comput. Sci. & Tech. | Imputation | Prototype memory of inter-series structure for irregular clinical series. |
| 2026 | [**TS-Memory** — Plug-and-Play Memory for TSFMs](https://scholar.google.com/scholar?q=%22TS-Memory%3A%20Plug-and-Play%20Memory%20for%20Time%20Series%20Foundation%20Models%22) | KDD | Forecasting | Distills an offline *k*-NN teacher into a memory adapter — **explicitly argues against retrieval**. |
| 2026 | [**MEMTS** — Parameterized Memory for Retrieval-Free Adaptation](https://doi.org/10.48550/arXiv.2602.13783) | arXiv | Forecasting | Internalizes domain knowledge so no datastore is searched at inference. |

<sub>[⬆ back to top](#top)</sub>

---

<a id="retrieval"></a>
## 🔎 Retrieval Memory

> Information is kept in an **external store** and a query-dependent subset is recalled at inference.

> ⚠️ **The central challenge: similarity ≠ predictive relevance.** Under non-stationarity, a highly similar example from an obsolete regime can be *less* useful than a moderately similar one from the current regime — and the higher the similarity score, the more confidently the model will be wrong.

<details>
<summary><b>Foundational retrieval work outside time series</b> (click to expand)</summary>

| Year | Method | Venue |
|---|---|---|
| 2020 | [**kNN-LM** — Generalization through Memorization](https://openreview.net/forum?id=HklBjCEKvH) | ICLR |
| 2020 | [**RAG** — Retrieval-Augmented Generation for Knowledge-Intensive NLP](https://scholar.google.com/scholar?q=%22Retrieval-augmented%20generation%20for%20knowledge-intensive%20NLP%20tasks%22) | NeurIPS |
| 2020 | [**REALM** — Retrieval-Augmented Language Model Pre-Training](https://scholar.google.com/scholar?q=%22Retrieval%20augmented%20language%20model%20pre-training%22) | ICML |

</details>

<a id="retrieval-instance"></a>
### 📦 Instance Retrieval Memory

Retains **concrete historical cases** — windows, trajectories, context–future pairs, examples from related series. The dominant form of retrieval memory in forecasting.

| Year | Method | Venue | Task | Memory & retrieval design |
|---|---|---|---|---|
| 2022 | [**MQ-ReTCNN** — Multi-Horizon Forecasting with Retrieval](https://scholar.google.com/scholar?q=%22MQ-ReTCNN%3A%20Multi-Horizon%20Time%20Series%20Forecasting%20with%20Retrieval-Augmentation%22) | KDD Workshop | Forecasting | Retrieves across *related entities* for multi-horizon forecasting. |
| 2022 | [**ReTime** — Retrieval Based Time Series Forecasting](https://arxiv.org/abs/2209.13525) | CIKM Workshop | Forecast. / Imput. | Earliest general **retrieve-then-synthesize** template; *relational retrieval* uses structural relations rather than sparse query values. |
| 2024 | [**RATSF** — Retrieval-Augmented TS Forecasting](https://arxiv.org/abs/2403.04180) | arXiv | Forecasting | Time-series knowledge base + retrieval-augmented **cross-attention**; targets strong non-stationarity. |
| 2024 | [**RATD** — Retrieval-Augmented Diffusion Models](https://scholar.google.com/scholar?q=%22Retrieval-augmented%20diffusion%20models%20for%20time%20series%20forecasting%22) | NeurIPS | Forecasting | Retrieved references **guide the denoising process** of a diffusion forecaster. |
| 2024 | [**RAF** — Retrieval Augmented TS Forecasting](https://arxiv.org/abs/2411.08249) | arXiv | Forecasting | Retrieval in the **zero-shot TSFM** setting; strategies for selecting related series. |
| 2025 | [**TimeRAG** — Boosting LLM Forecasting via RAG](https://doi.org/10.1109/ICASSP49660.2025.10889933) | ICASSP | Forecasting | Converts retrieved sequences into **context for an LLM** forecaster. |
| 2025 | [**RAFT** — Retrieval Augmented Time Series Forecasting](https://proceedings.mlr.press/v267/han25d.html) | ICML | Forecasting | Multi-period Pearson correlation over patches; augments the query with **realized futures**. |
| 2025 | [**TimeRAF** — Retrieval-Augmented Foundation Model](https://doi.org/10.1109/TKDE.2025.3579137) | IEEE TKDE | Forecasting | Learns the retriever **end-to-end**; channel prompting for integration. |
| 2025 | [**TS-RAG** — RAG-Based TSFMs are Stronger Zero-Shot Forecasters](https://doi.org/10.52202/085713-5448) | NeurIPS | Forecasting | **Adaptive Retrieval Mixer** weights retrieved contexts and their future horizons. |
| 2025 | [**LLMAD** — Accurate & Interpretable TS Anomaly Detection](https://doi.org/10.1145/3711896.3737239) | KDD | Anomaly det. | Retrieves historical **positive/negative** cases as in-context evidence. |
| 2025 | [**RATFM** — Retrieval-Augmented TS Foundation Model](https://arxiv.org/abs/2506.02081) | arXiv | Anomaly det. | In-domain **normal** examples for test-time adaptation. |
| 2026 | [**RAST** — Retrieval Augmented Spatio-Temporal Framework](https://ojs.aaai.org/index.php/AAAI/article/view/41264) | AAAI | Forecasting | Fine-grained spatiotemporal patterns; separate spatial and temporal query construction. |
| 2026 | [**SpecReTF** — Spectral Retrieval-Augmented Forecasting](https://arxiv.org/abs/2606.19412) | arXiv | Forecasting | **Frequency-aware** retrieval with temporal recency. |
| 2026 | [**CRAFT** — Channel-Wise Retrieval for Multivariate Forecasting](https://doi.org/10.1109/ICASSP55912.2026.11463178) | ICASSP | Forecasting | Extends spectral matching to **multivariate channel structure**. |

<a id="retrieval-latent"></a>
### 🧊 Latent Retrieval Memory

Retains **compact derived representations** — embeddings, latent states, learned tokens, priors — as first-class entries. Reserved for systems where the *encoded object itself* is the persistent, recalled information.

| Year | Method | Venue | Task | Why latent rather than raw |
|---|---|---|---|---|
| 2026 | [**ALER-TI** — Aligned Latent Embedding Retrieval for Imputation](https://arxiv.org/abs/2607.07640) | arXiv | Imputation | Query and candidate differ in **missingness pattern**, so observation-space similarity is ill-defined; post-hoc masking in latent space makes cached complete embeddings comparable with corrupted queries. |
| 2026 | [**ReDiTT** — Retrieval-Augmented Conditional Diffusion Transformers](https://arxiv.org/abs/2607.12391) | arXiv | Async. events | Raw asynchronous events mix continuous inter-event times, discrete types, variable lengths and padding; latent tokens in a reference bank feed a conditional diffusion transformer through cross-attention. |

> **Boundary rule.** If a method encodes entries only as a *search key* but returns the original historical case, that is **instance retrieval with latent search** — not latent retrieval memory.

<a id="retrieval-knowledge"></a>
### 📖 Knowledge Retrieval Memory

Stores information that is **not another realization of the target series** — text, semantic descriptions, exogenous events, metadata, structured knowledge. It provides context *about the process* rather than another trajectory to imitate.

| Year | Method | Venue | Task | Knowledge source |
|---|---|---|---|---|
| 2024 | [**EMERGE** — RAG for Multimodal EHR Predictive Modeling](https://doi.org/10.1145/3627673.3679582) | CIKM | Clinical | Links longitudinal observations and clinical text to **biomedical knowledge graphs**. |
| 2024 | [**REALM** — RAG-Driven Enhancement of Multimodal EHR Analysis](https://arxiv.org/abs/2402.07016) | arXiv | Clinical | Retrieves KG entities and relations alongside multimodal records. |
| 2025 | [**TRACE** — Grounding Time Series in Context](https://scholar.google.com/scholar?q=%22TRACE%3A%20Grounding%20Time%20Series%20in%20Context%20for%20Multimodal%20Embedding%20and%20Retrieval%22) | NeurIPS | Retrieval / Cls. | Channel-level TS↔text alignment with hard-negative mining; supports **bidirectional** TS-to-text and text-to-TS retrieval. |
| 2026 | [**SERAF** — Semantics-Enhanced Retrieval-Augmented Forecasting](https://arxiv.org/abs/2606.14941) | ICML Workshop | Forecasting | **Derives** its own text descriptions, runs parallel numerical *and* semantic retrievals, then selectively combines the futures. |

> Main difficulty: **temporal and semantic alignment**. Retrieved knowledge must match the right entity and variable *and* remain valid at the relevant time — stale context misleads exactly as an outdated historical trajectory does.

<sub>[⬆ back to top](#top)</sub>

---

<a id="agentic"></a>
## 🤖 Agentic Memory

> An external store whose **write, read, and forget** policies are decided by a controller, so the store evolves across interactions.

**Functional types**

| Type | Target question | Stored content | Written | Read at | Decay |
|---|---|---|---|---|---|
| 🎬 **Episodic** | *What happened in similar cases before?* | Context, forecast, error triples | per instance | prediction, reflection | **fast** |
| 📚 **Semantic** | *What is generally true of this series?* | Regimes, calendar rules, exogenous facts | once evidence accrues | prediction, scoring | medium |
| 🛠️ **Procedural** | *How should I go about forecasting this?* | Tool trajectories, model-selection playbooks | once evidence accrues | planning, action | **slow** |

### 📋 Systems at a Glance

*Type:* **E** episodic · **S** semantic · **P** procedural · **W** working (within-episode). *Scope:* per-sequence (resets) · per-dataset (distilled offline, frozen at test) · online. *Forget:* an explicit revision, demotion, or validity-decay policy.

| System | Type | Memory content | Write policy | Scope | Forget | Task |
|---|:---:|---|---|---|:---:|---|
| [**MemCast**](https://arxiv.org/abs/2602.03164) | E, S, P | Patterns, wisdom, laws | Offline distillation + reflection | per-dataset | ✅ conf. decay | Forecasting |
| [**Cast-R1**](https://arxiv.org/abs/2602.13802) | W | Decision-relevant state | RL-learned, multi-turn | per-sequence | ❌ | Forecasting |
| [**CastFlow**](https://arxiv.org/abs/2604.27840) | P | Tool-use trajectories | Sampled successful paths | per-dataset | ❌ | Forecasting |
| [**Nexus**](https://arxiv.org/abs/2605.14389) | E, S | Error signatures, guidelines | Backtest calibration | per-dataset | ❌ | Forecasting |
| [**AlphaCast**](https://arxiv.org/abs/2511.08947) | E, S | Historical cases, domain knowledge | Context / case retrieval | per-sequence | ❌ | Forecasting |
| [**Argos**](https://arxiv.org/abs/2501.14170) | S | Anomaly **rules** | Multi-agent generate & repair | **online** | ✅ rule revision | Anomaly det. |
| [**AnomaMind**](https://arxiv.org/abs/2602.13807) | S, P | Anomaly patterns, domain knowledge | Offline pattern mining | per-dataset | ❌ | Anomaly det. |
| [**Agentic-RAG**](https://arxiv.org/abs/2408.14484) | P | Prompt pool of skills | Learned, hierarchical | per-dataset | ❌ | All four |

<a id="agentic-episodic"></a>
### 🎬 Episodic & Working Stores

**Persistent episodic** — records specific past events and their outcomes.

- [**MemCast**](https://arxiv.org/abs/2602.03164) — distills training data into a hierarchical store whose lowest tier summarizes prediction outcomes into *historical patterns*, with a **per-entry confidence score updated dynamically** as the entry proves useful (so the store evolves at inference while explicitly avoiding test-data leakage).
- [**Nexus**](https://arxiv.org/abs/2605.14389) — domain-level calibration loop scoring past forecasts against ground truth across historical splits; a guideline is written **only if it improves held-out accuracy beyond a threshold** — a genuinely selective write.
- [**AlphaCast**](https://arxiv.org/abs/2511.08947) — historical cases plus domain knowledge and context for iterative forecasting, but assembled *per interaction* rather than maintained as a persistent evolving store.

**Working memory** — state retained only for the duration of a single analysis. Excluded from the functional-type table: it cannot decay under regime change because it does not outlive the episode that created it.

- [**Cast-R1**](https://arxiv.org/abs/2602.13802) — *useful boundary case:* a learned policy decides what evidence to retain across reasoning steps, but the state stays confined to one forecasting episode. Agentic **state management** without persistent memory.
- [**ReasonTSC**](https://arxiv.org/abs/2506.00807) — accumulates reasoning state before fusing it into a classification decision.
- [**TS-Reasoner**](https://openreview.net/forum?id=yhy7Vigjcf) · [**TimeCopilot**](https://openreview.net/forum?id=A11xyQvSmT) · [**TimeSeriesScientist**](https://arxiv.org/abs/2510.01538) · [**ColaCare**](https://scholar.google.com/scholar?q=%22Colacare%3A%20Enhancing%20electronic%20health%20record%20modeling%20through%20large%20language%20model-driven%20multi-agent%20collaboration%22) — retain intermediate findings and tool outputs within a single run.

<a id="agentic-semantic"></a>
### 📚 Semantic Stores

Distill reusable knowledge; they differ mainly in what that knowledge is asked to *do*.

- [**Argos**](https://arxiv.org/abs/2501.14170) — turns stored knowledge into **executable rules**. Each rule is a function classifying a window as normal or anomalous, so knowledge is validated *by running it on data*. Collaborating agents write rules, **repair** syntax and runtime errors, and score on validation before admission; a review agent derives **revisions** rather than discarding on regression. The only surveyed system that revises its store after deployment, and the repository stays inspectable for operators.
- [**AnomaMind**](https://arxiv.org/abs/2602.13807) — anomaly-type descriptions and visual prototypes as external knowledge, mined offline and consulted alongside numerical diagnostics; informs reasoning without serving as the final decision rule.
- [**MemCast**](https://arxiv.org/abs/2602.03164) — induces *general laws* from extracted temporal features and applies them as criteria for reflective iteration.

<a id="agentic-procedural"></a>
### 🛠️ Procedural Stores

Capture **how to act**.

- [**CastFlow**](https://arxiv.org/abs/2604.27840) — a *strategy memory* pairing each lookback window with its best-performing tool schedule; the agent selects diagnostics by similarity to past windows instead of scheduling zero-shot. Populated by expanding each training instance into parallel exploration paths and archiving only the best-scoring trajectory. Reports a **sweet spot in how many trajectories to recall** — too few under-guides the agent, too many injects redundant context that degrades reasoning.
- [**MemCast**](https://arxiv.org/abs/2602.03164) (middle tier) — *reasoning wisdom* distilled from inference trajectories, used to select among candidate reasoning paths.
- [**Agentic-RAG**](https://arxiv.org/abs/2408.14484) — reusable skills as a hierarchical **prompt pool** indexed by task, with a master agent routing to sub-agents. ⚠️ Built from **successful runs only** — it records what worked but cannot warn against reusing a strategy in a regime where it failed.

<sub>[⬆ back to top](#top)</sub>

---

<a id="benchmarks"></a>
## 🧪 Benchmarks, Evaluation & Resources

Benchmark families, ordered by how far out the memory spectrum they push. † = emerging non-archival or workshop benchmark, included where no mature archival equivalent exists.

| Family | Resources | What it stresses |
|---|---|---|
| **General** | [TFB](https://doi.org/10.14778/3665844.3665863) · [Monash Archive](https://scholar.google.com/scholar?q=%22Monash%20Time%20Series%20Forecasting%20Archive%22) · [UCR Archive](https://doi.org/10.1109/JAS.2019.1911747) · [TSB-AD](https://doi.org/10.52202/079017-3437) | Standard forecasting, classification, anomaly detection across heterogeneous datasets. |
| **Multivariate** | [BasicTS+](https://doi.org/10.1109/TKDE.2024.3484454) | Cross-variable dependencies — tests shared and cross-variable memory. |
| **Spatiotemporal** | [LargeST](https://scholar.google.com/scholar?q=%22LargeST%3A%20A%20Benchmark%20Dataset%20for%20Large-Scale%20Traffic%20Forecasting%22) | Long temporal coverage, many sensors, metadata — memory across many related entities. |
| **Hierarchical** | [M5](https://doi.org/10.1016/j.ijforecast.2021.11.013) | Sparse product–store series across multiple aggregation levels. |
| **Foundation model** | [ProbTS](https://doi.org/10.52202/079017-1523) · [Chronos](https://openreview.net/group?id=TMLR) · [BOOM](https://scholar.google.com/scholar?q=%22This%20Time%20is%20Different%3A%20An%20Observability%20Perspective%20on%20Time%20Series%20Foundation%20Models%22) | Broad-horizon, zero-shot, large-scale evaluation of universal forecasters. |
| **Multimodal** | [Time-MMD](https://doi.org/10.52202/079017-2476) · [CiK](https://proceedings.mlr.press/v267/williams25a.html) · [Time-IMM](https://scholar.google.com/scholar?q=%22Time-IMM%3A%20A%20Dataset%20and%20Benchmark%20for%20Irregular%20Multimodal%20Multivariate%20Time%20Series%22) · [TRACE](https://scholar.google.com/scholar?q=%22TRACE%3A%20Grounding%20Time%20Series%20in%20Context%20for%20Multimodal%20Embedding%20and%20Retrieval%22) | Series paired with text, context, irregular modalities, cross-modal retrieval targets. |
| **Reasoning** | [TimeSeriesExamAgent](https://iclr.cc/virtual/2026/poster/10010760) · [EngineMT-QA](https://scholar.google.com/scholar?q=%22ITFormer%3A%20Bridging%20Time%20Series%20and%20Natural%20Language%20for%20Multi-Modal%20QA%20with%20Large-Scale%20Multitask%20Dataset%22) · [Time-MQA](https://doi.org/10.18653/v1/2025.acl-long.1437) · [ChatTS](https://doi.org/10.14778/3742728.3742735) | Pattern understanding, QA, explanation, multi-step temporal reasoning. |
| **Agentic** | [MAFS](https://scholar.google.com/scholar?q=%22Many%20Minds%2C%20One%20Goal%3A%20Time%20Series%20Forecasting%20via%20Sub-task%20Specialization%20and%20Inter-agent%20Cooperation%22) · [TimeSeriesGym](https://openreview.net/forum?id=8M3qAX6e5M)† | Agent cooperation and end-to-end time-series ML workflows. Persistent-memory evaluation remains immature. |
| **Memory-specific** | [SynTSBench](https://scholar.google.com/scholar?q=%22SynTSBench%3A%20Rethinking%20Temporal%20Pattern%20Learning%20in%20Deep%20Learning%20Models%20for%20Time%20Series%22) · [TS-Haystack](https://openreview.net/forum?id=vGMayMuH83)† | Controlled temporal capability tests; long-context retrieval as context length grows. |

### 📏 What Memory-Specific Evaluation Should Report

Standard task accuracy conflates the store, the access mechanism, and the predictor. We recommend reporting four quantities **separately from final task error**:

1. **Retention** — does a known informative event remain recoverable as its temporal distance from the query grows?
2. **Retrieval quality** — does the relevant record appear among the retrieved items? *With random, no-retrieval, and where possible oracle controls.*
3. **Temporal validity** — does the model reject or down-weight memories from obsolete regimes?
4. **Memory management** — score write, update, and forget decisions over repeated episodes **under a fixed storage budget**.

> ⚠️ Current agentic evaluations compare *complete systems*, not an otherwise identical agent **with and without** persistent memory. The evidence supports the effectiveness of memory-enabled agentic systems, but not yet the **causal contribution** of their write, recall, update, and forget operations.

### 🛠️ Open-Source Libraries

| Library | Focus |
|---|---|
| [**GluonTS**](https://www.jmlr.org/papers/v21/19-820.html) | Probabilistic forecasting and anomaly detection; reference implementations and evaluation tools. |
| [**aeon**](https://www.jmlr.org/papers/v25/23-1444.html) | Unified forecasting, classification, regression, clustering, similarity search. |
| [**Merlion**](https://jmlr.org/papers/v24/22-0809.html) | Forecasting and anomaly-detection pipelines with deployment-oriented evaluation. |

<sub>[⬆ back to top](#top)</sub>

---

<a id="surveys"></a>
## 📚 Related Surveys

Coverage of the memory spectrum: ✅ substantial · 🟡 partial · ⬜ little/none. **#** ≈ number of surveyed works.

| Survey | Internal | [Explicit](#explicit) | [Retrieval](#retrieval) | [Agentic](#agentic) | Problem view |
|---|:---:|:---:|:---:|:---:|:---:|
| [Time-Series Forecasting with Deep Learning](https://scholar.google.com/scholar?q=%22Time-series%20forecasting%20with%20deep%20learning%3A%20a%20survey%22) | ✅ | 🟡 | ⬜ | ⬜ | ⬜ |
| [Transformers in Time Series](https://scholar.google.com/scholar?q=%22Transformers%20in%20time%20series%3A%20A%20survey%22) | 🟡 | ⬜ | ⬜ | ⬜ | ⬜ |
| [Graph Neural Networks for Time Series](https://scholar.google.com/scholar?q=%22A%20survey%20on%20graph%20neural%20networks%20for%20time%20series%3A%20Forecasting%2C%20classification%2C%20imputation%2C%20and%20anomaly%20detection%22) | 🟡 | 🟡 | ⬜ | ⬜ | ⬜ |
| [Foundation Models for Time Series](https://scholar.google.com/scholar?q=%22Foundation%20models%20for%20time%20series%20analysis%3A%20A%20tutorial%20and%20survey%22) | 🟡 | ⬜ | 🟡 | 🟡 | ⬜ |
| [Reasoning and Agentic Systems in Time Series with LLMs](https://scholar.google.com/scholar?q=%22A%20Survey%20of%20Reasoning%20and%20Agentic%20Systems%20in%20Time%20Series%20with%20Large%20Language%20Models%22) | ⬜ | ⬜ | 🟡 | ✅ | 🟡 |
| **➡️ This survey** | 🟡 | ✅ | ✅ | ✅ | ✅ |

**Adjacent memory surveys** — [Memory in the Age of AI Agents](https://arxiv.org/abs/2512.13564) · [Cognitive Architectures for Language Agents](https://scholar.google.com/scholar?q=%22Cognitive%20Architectures%20for%20Language%20Agents%22) · [Agentic RAG: A Survey](https://arxiv.org/abs/2501.09136)

**Agent-memory mechanisms inherited from NLP** — [Reflexion](https://scholar.google.com/scholar?q=%22Reflexion%3A%20Language%20Agents%20with%20Verbal%20Reinforcement%20Learning%22) (verbal self-reflection) · [ExpeL](https://scholar.google.com/scholar?q=%22ExpeL%3A%20LLM%20Agents%20Are%20Experiential%20Learners%22) (insight distillation) · [Agent Workflow Memory](https://arxiv.org/abs/2409.07429) (workflow abstraction) · [Position: Beyond Model-Centric Prediction](https://arxiv.org/abs/2602.01776) (forecasting as an agentic process)

<sub>[⬆ back to top](#top)</sub>

---

<a id="contributing"></a>
## 🤝 Contributing

Found a paper that should be here? Contributions are especially welcome for papers introducing **new mechanisms for storing, retrieving, or managing temporal information** — especially retrieval for classification and imputation, explicit and retrieval memory for decision-making, and agentic memory beyond forecasting.

**How to add a paper**

1. ⭐ Star the repository.
2. 🔀 Open a pull request adding a row to the right subsection table, or 🐛 open an issue with the reference.
3. Please fill in every column so the entry stays useful:

| Column | What to put |
|---|---|
| **Year** | Publication year of the cited version |
| **Method** | `[**Name** — Title](link)` — link to the paper (DOI, arXiv, or proceedings) |
| **Venue** | Conference or journal, or `arXiv` for preprints |
| **What the memory holds** | One sentence on the **stored representation** and the **read/write semantics** — not a summary of the results |

**Placement guide** — classify by *what is retained and how it is accessed*, not by backbone:

- A bounded module learned jointly with the model → [Explicit](#explicit), even if its read looks like retrieval.
- An unbounded external store with predefined or appended contents and a query-dependent read → [Retrieval](#retrieval).
- A controller decides what is written, revised, promoted, or forgotten → [Agentic](#agentic).
- Stores an encoded object as a *search key* but returns the original case → **instance** retrieval, not [latent](#retrieval-latent).

<sub>[⬆ back to top](#top)</sub>

---

<a id="citation"></a>
## 📌 Citation

If you find this collection useful, please cite the survey:



<sub>Paper links point to the DOI, arXiv, or proceedings entry recorded in the survey's <code>references.bib</code>; where no identifier was recorded, the link is a Google Scholar title search.</sub>

<sub>[⬆ back to top](#top)</sub>
