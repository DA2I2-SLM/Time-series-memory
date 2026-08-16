# Time-series-memory

A curated collection of research on **memory mechanisms for time-series modeling**, organized into four categories: **Internal, Explicit, Retrieval, and Agentic Memory**.

---

## 🗂️ Taxonomy

| Category | Memory Mechanism | Key Idea |
|:---:|---|---|
| 🧠 **Internal** | Latent state / parameterized memory | Compress history into model parameters or hidden states |
| 💾 **Explicit** | Persistent memory | Store historical information in dedicated memory structures |
| 🔎 **Retrieval** | Addressable memory | Retrieve relevant historical information when needed |
| 🤖 **Agentic** | Managed memory | Actively store, retrieve, update, and forget information |

---

## 🧠 Internal Memory

### 🔄 Recurrent State

| Year | Title | Authors | Venue | Note |
|---|---|---|---|---|
| 1990 | *Finding Structure in Time* | Jeffrey L. Elman | Cognitive Science | Introduces recurrent neural networks as a mechanism for learning temporal representations. |
| 1997 | *Long Short-Term Memory* | Sepp Hochreiter, Jürgen Schmidhuber | Neural Computation | Introduces LSTM with gated memory for preserving information over long horizons. |
| 2014 | *Learning Phrase Representations using RNN Encoder–Decoder for Statistical Machine Translation* | Kyunghyun Cho et al. | EMNLP | Introduces GRU, a simplified gated recurrent architecture. |
| 2015 | *Long Short Term Memory Networks for Anomaly Detection in Time Series* | Pankaj Malhotra et al. | ESANN | Uses LSTM representations to model normal temporal behavior for anomaly detection. |
| 2017 | *A Dual-Stage Attention-Based Recurrent Neural Network for Time Series Prediction* | Yao Qin et al. | IJCAI | Combines recurrent encoder–decoder states with input and temporal attention. |
| 2018 | *Modeling Long- and Short-Term Temporal Patterns with Deep Neural Networks* | Guokun Lai et al. | SIGIR | Combines convolutional and recurrent components with skip connections and autoregression for long- and short-term temporal patterns. |
| 2018 | *Recurrent Neural Networks for Multivariate Time Series with Missing Values* | Zhengping Che et al. | Scientific Reports | Incorporates missingness patterns and time intervals into recurrent state updates. |
| 2018 | *BRITS: Bidirectional Recurrent Imputation for Time Series* | Wei Cao et al. | NeurIPS | Uses bidirectional recurrent dynamics for iterative time-series imputation. |
| 2018 | *Attend and Diagnose: Clinical Time Series Analysis Using Attention Models* | Edward Choi et al. | — | Uses recurrent representations and attention to identify informative variables and temporal patterns in clinical time series. |
| 2019 | *Multivariate LSTM-FCNs for Time Series Classification* | Karim Fazle et al. | Neural Networks | Combines LSTM and convolutional representations for multivariate time-series classification. |
| 2020 | *DeepAR: Probabilistic Forecasting with Autoregressive Recurrent Networks* | David Salinas et al. | International Journal of Forecasting | Uses autoregressive recurrent states to model temporal patterns across related series. |
| 2021 | *CloudLSTM: A Recurrent Neural Model for Spatiotemporal Point-Cloud Stream Forecasting* | — | — | Extends recurrent memory to spatiotemporal point-cloud streams. |
| 2025 | *Unlocking the Potential of LSTMs for Long-Term Time Series Forecasting* | — | — | Revisits LSTMs for modern long-term forecasting and demonstrates the continued potential of recurrent memory. |

### 🌀 Compressive State

| Year | Title | Authors | Venue | Note |
|---|---|---|---|---|
| 2022 | *Efficiently Modeling Long Sequences with Structured State Spaces* | Albert Gu et al. | ICLR | Introduces S4, using structured state-space dynamics for efficient long-range sequence modeling. |
| 2022 | *FiLM: Frequency Enhanced Legendre Memory Model for Long-term Time Series Forecasting* | Haixu Zhou et al. | NeurIPS | Combines structured state representations with frequency-domain information for long-term forecasting. |
| 2023 | *Mamba: Linear-Time Sequence Modeling with Selective State Spaces* | Albert Gu, Tri Dao | COLM | Introduces input-dependent selective state transitions that adaptively retain or discard information. |
| 2024 | *TimeMachine: A Time Series is Worth 4 Mambas for Long-term Forecasting* | — | ICLR | Explores Mamba-based components for long-term time-series forecasting. |
| 2024 | *MambaTS: Improved Selective State Space Models for Long-term Time Series Forecasting* | — | — | Adapts selective state-space modeling to long-term temporal dependencies. |
| 2024 | *SST: Multi-Scale Mamba for Time Series Forecasting* | — | — | Combines Mamba and Transformer experts across multiple temporal scales. |
| 2025 | *S-Mamba: Selective State Space Models for Time Series* | — | — | Empirically studies selective state-space compression for time-series modeling. |

---

## 💾 Explicit Memory

- 📄 **[Paper]** *...*
- 📄 **[Paper]** *...*
- 📄 **[Paper]** *...*

---

## 🔎 Retrieval Memory

- 📄 **[Paper]** *...*
- 📄 **[Paper]** *...*
- 📄 **[Paper]** *...*

---

## 🤖 Agentic Memory

- 📄 **[Paper]** *...*
- 📄 **[Paper]** *...*
- 📄 **[Paper]** *...*

---

## 📚 Related Surveys

- 📖 **[Survey]** *Time Series Forecasting: A Survey of the State-of-the-Art*
- 📖 **[Survey]** *Deep Learning for Time Series Forecasting: A Survey*
- 📖 **[Survey]** *Foundation Models for Time Series: A Survey*
- 📖 **[Survey]** *Large Language Models for Time Series: A Survey*
- 📖 **[Survey]** *Time Series Foundation Models: A Survey*

---

## 🤝 Contributing

Found a paper that should be included?

- ⭐ Star the repository
- 🐛 Open an issue
- 🔀 Submit a pull request

Contributions are welcome, especially papers that introduce **new mechanisms for storing, retrieving, or managing temporal information**.

---

## 📌 Citation

If you find this repository useful, please consider citing our survey:

If you know of a paper that should be included, please open an issue or submit a pull request.

Citation
