# Zeru-V2
Methodology Document: Compound V2 Wallet Credit Scoring
Introduction
The goal of this project was to develop a credit scoring system that assigns a score between 0 and 100 to each wallet interacting with the Compound V2 protocol. The score reflects the reliability and riskiness of wallet behavior based solely on historical transaction data. Higher scores indicate responsible, consistent usage, while lower scores suggest risky, bot-like, or exploitative behavior.

Data Preparation
I began with raw transaction-level JSON data from Compound V2, containing multiple files each representing different action types such as deposits, borrows, repays, withdrawals, and liquidations. Each record included wallet addresses, transaction amounts (in tokens and USD), asset information, timestamps, and transaction hashes.

To prepare the data, I wrote scripts to parse and flatten the JSON files into a unified tabular format. I extracted relevant fields and added an explicit “action” column based on the source file to preserve transaction type information. After concatenating data from multiple files, I performed thorough cleaning by converting data types, handling missing values, and filtering out incomplete records. The cleaned dataset was saved as a CSV file to facilitate efficient downstream processing.

Feature Engineering
Feature engineering was central to capturing nuanced wallet behavior beyond simple aggregate statistics. I designed a comprehensive set of wallet-level features to characterize activity patterns, risk indicators, and network relationships.

First, I calculated basic aggregates such as total transaction count, unique assets interacted with, total and average USD transaction volume, and active days. To capture temporal dynamics, I implemented sessionization by grouping transactions into sessions separated by gaps longer than one hour. From this, I derived session counts and statistics on transactions per session, which help distinguish steady users from bursty or opportunistic actors.

I also computed recency metrics, such as the ratio of transactions in the last 30 days to total transactions, to reward ongoing engagement. To detect potential flash loan or bot activity, I developed a heuristic flash loan score based on the sum of large transactions occurring within very short time gaps.

Network features were extracted by constructing a bipartite graph linking wallets and assets. From this graph, I calculated degree centrality to identify influential wallets and a clique score to measure the wallet’s participation in tightly connected subgraphs, which can indicate coordinated or anomalous behavior.

Additionally, I counted the number of transactions per action type per wallet to understand behavioral diversity.

Modeling and Scoring Approach
Given the unsupervised nature of the problem and absence of labeled data, I combined clustering with a rule-based scoring system to balance data-driven insights and interpretability.

I applied KMeans clustering on selected behavioral features to group wallets into behaviorally similar cohorts. This clustering provided a lens to understand distinct wallet usage patterns and supported explainability.

The credit score for each wallet was computed as a weighted sum of standardized features, including transaction counts, volume, diversity, session metrics, recency, network centrality, and flash loan penalties. The weights were chosen based on domain knowledge and exploratory analysis, emphasizing features indicative of responsible behavior while penalizing suspicious activity. A sigmoid transformation was applied to the raw score to normalize it to a 0–100 scale with natural decay, preventing score inflation.

This hybrid approach ensures that the scoring system is transparent, adjustable, and grounded in meaningful behavioral signals.

Advantages of This Approach
This methodology goes beyond simplistic aggregate metrics by incorporating temporal, network, and behavioral dimensions. Sessionization and recency capture engagement patterns over time, while graph features reveal relational context often missed by tabular analysis. The flash loan heuristic targets a common exploit vector in DeFi, enhancing risk detection.

The combination of clustering and rule-based scoring balances pattern discovery with explainability, making the model both insightful and practical for protocol risk management. The approach is fully reproducible from raw data without reliance on external labels or pretrained models, meeting the challenge constraints.

Potential Improvements with More Time
If more time were available, I would explore several enhancements:

Advanced Time-Series Modeling: Implement recurrent neural networks or transformer-based models to capture complex temporal dependencies in wallet behavior.

Graph Neural Networks: Leverage GNNs to learn richer wallet embeddings from the transaction graph, potentially improving anomaly detection and scoring accuracy.

Anomaly Detection Models: Integrate unsupervised anomaly detection algorithms like Isolation Forest or Autoencoders on engineered features to better isolate bots or exploiters.

Feature Expansion: Incorporate on-chain metrics such as gas usage patterns, token price volatility, or cross-protocol interactions.

Explainability Tools: Use SHAP or LIME to provide per-wallet feature importance explanations for greater transparency.

Continuous Learning: Design a pipeline for incremental updates as new transaction data arrives, maintaining up-to-date wallet scores.

Conclusion
This project demonstrates a comprehensive, explainable approach to credit scoring in DeFi using raw transaction data. By combining rich feature engineering, behavioral clustering, and a transparent scoring formula, the system effectively distinguishes reliable users from risky actors. The methodology is robust, reproducible, and extensible, providing a strong foundation for decentralized credit risk assessment.
