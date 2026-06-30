# AI Course — Ramat Gan

**Author:** Yair Levi
**Director:** Yoram Segal, PhD
**College:** OASIS Capital Israel (היחידה ללימודי חוץ)

This course covers the foundations of **Linear Algebra and Statistics** applied to Machine Learning and AI, progressing from first-principles math to neural networks, reinforcement learning, computer vision, NLP, and AI agents. Below is a summary of the course built from each exercise's own README.

## Exercises

1. **Rotating Rectangle** — 2D rotation matrices and homogeneous coordinates animated with NumPy/Matplotlib.
2. **Linear Regression by Random Sampling** — Monte Carlo line-fitting vs. analytical least squares; vectorized error computation.
3. **Linear Regression via Dot Product** — Coefficient estimation using explicit `np.dot` formulas, connecting linear algebra to OLS.
4. **Multiple Regression: R² vs Adjusted R²** — Demonstrates multicollinearity effects and overfitting penalties across 50–55 predictors.
5. **Sinusoidal Peak Detection** — Convolution-based template matching for signal processing.
6. **Association Rules Mining (synthetic)** — Manual support/confidence/lift calculations on binary data.
7. **Grocery Association Rules Mining** — Apriori-style 3→2 item rule mining on a real 9,835-transaction dataset.
8. **Cluster Visualization** — Gaussian cluster generation, 2σ ellipses, and K-Means comparison.
9. **Semantic Clustering** — Sentence embeddings, K-Means, and KNN classification of generated text.
10. **PCA & t-SNE Text Vectorization** — Manual PCA (eigendecomposition) vs. sklearn PCA vs. t-SNE on sentence embeddings.
11. **Logistic Regression with Gradient Descent** — From-scratch binary classifier with sigmoid, gradient ascent, and decision boundaries.
12. **Exercise Checking System** — Multi-agent pipeline (Gmail retrieval, code grading, styled feedback, draft emails) — early **AI Agents** exercise.
13. **Iris Naive Bayes Classification** — Manual vs. scikit-learn Bayes classifiers using feature histograms.
14–16. **Context Window / "Lost in the Middle" Experiments** — Testing LLM (Claude) retrieval accuracy vs. document position and size; RAG vs. context-window comparison.
17. **Iris SVM Classification** — Hierarchical two-stage SVM for multi-class separation.
18. **DeepFake Video Detection** — Heuristic multi-criteria video analysis (facial, temporal, lighting, geometry) plus ML detector scaffolding.
19. **Perceptron Gates (AND/XOR)** — Keras perceptrons showing linear vs. non-linear separability.
20. **Finds a Rule from Data** — Single- vs. multi-neuron linear regression discovering the Celsius→Fahrenheit rule via backpropagation.
21. **Discrete Update Algorithm** — Manual perceptron learning rule solving the AND gate.
22. **Balanced Token Tree** — Binary tree token-balancing algorithm with visualization.
23. **BST Distributed DeepFake Detection** — Same deepfake pipeline re-architected as a 31-node distributed binary-search-tree system — **AI Agents** design with decision escalation and aggregation.
24. **Image Frequency Filter** — FFT-based HPF/LPF/BPF filtering of images.
25. **Triangle Edge Detection** — FFT edge detection + Hough transform + geometric vertex reconstruction.
26. **JPEG Compression Analysis** — Quality vs. file-size vs. MSE trade-off study.
27. **Video Processing Analysis** — Metadata/GOP analysis, motion-vector visualization, object overlay.
28. **Diabetic Retinopathy Classification** — Transfer learning (ResNet-50) with progressive fine-tuning stages.
29. **AutoEncoder — Cats & Dogs** — Convolutional autoencoders with cross-domain encoder/decoder swapping.
30. **Autoencoder Image Denoising** — U-Net-style denoising autoencoder with an extensive debugging case study.
31. **RL Drone (Q-Table)** — Q-learning with eligibility traces, reward shaping, optimistic initialization, and Double Q-learning, visualized live.
32. **RNN Word Prediction** — LSTM next-word prediction with biased synthetic sentence generation and full evaluation metrics.
33. **LSTM Signal Filter** — Dual-input LSTM separating one target sine wave from a noisy multi-frequency composite.
34. **Positional Encoding** — Notes on Transformer positional encoding principles.
35. **Dueling DQN Stock Advisor** — Deep Q-Network trading agent on Yahoo Finance data — **AI Agents** application.
36. **ParkGuard AI** — Multi-agent system combining DVR video ingestion, MediaPipe detection, a local LLM (Ollama) for conversational zone selection, and dual-channel (Gmail/Telegram) alerting — capstone **AI Agents** project.

## Recurring Themes

- **Linear algebra:** dot products, rotation matrices, eigendecomposition, covariance.
- **Statistics:** regression, R²/Adjusted R², Bayes, hypothesis testing (context-window experiments).
- **Classical ML:** K-Means, KNN, SVM, Naive Bayes, association rules.
- **Deep learning:** perceptrons, CNNs, autoencoders, RNN/LSTM, transfer learning.
- **Reinforcement learning:** Q-learning, Dueling DQN.
- **Signal/image/video processing:** FFT filtering, convolution, JPEG compression, motion vectors.
- **AI Agents:** multi-agent pipelines, LLM-driven decision making, distributed agent architectures.
