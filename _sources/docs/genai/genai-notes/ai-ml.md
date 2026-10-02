# Artificial Intelligence (AI)

Artificial Intelligence (AI) is the broad field: building systems that perform tasks which normally require human intelligence — reasoning, understanding language, recognizing patterns, making decisions, etc. AI is the umbrella goal, not a specific technique. It includes rule-based expert systems, search algorithms, robotics, computer vision, NLP, and machine learning.

## Machine Learning

**Machine Learning (ML)** is one approach _within_ AI. Instead of hand-coding rules (like `if-else` logic), you feed a system data and let it learn patterns/statistical relationships on its own, then use that learned model to make predictions on new data.

In Machine Learning typically are two phases

1. **Training**: The model learns from data.
2. **Inference**: The trained model makes predictions on new, unseen data.

![Machine Learning Model](/_static/images/GenAI_MachineLearningModel.png)

**Key difference, in a nutshell:**

|                           | AI                                                 | ML                                                |
| ------------------------- | -------------------------------------------------- | ------------------------------------------------- |
| Scope                     | Broad goal — "make machines act intelligently"     | Subset of AI — "learn from data"                  |
| Approach                  | Can be rule-based, search-based, or learning-based | Always data-driven; model improves with more data |
| Example (non-learning AI) | A chess engine using minimax + heuristics          | A spam filter that learns from labeled emails     |

**Analogy for a dev mindset:**
Think of AI like an interface — it defines _what_ the system should accomplish (intelligent behavior). ML is one _implementation_ of that interface, using statistics and data instead of explicitly written logic. A rule engine (`if amount > 10000 && country == "high-risk" → flag`) is AI without ML. A fraud model trained on millions of past transactions to predict "flag/not-flag" is ML.

So the nesting is:

```
AI ⊃ ML ⊃ Deep Learning
```

There are two branches in Machine Learning.

### 1. Statistical ML

- Linear Algorithms
- Regression Algorithms
- Decision Tree Algorithms
- K-means Algorithms

### 2. Deep Learning

Deep Learning (DL) is a further subset of ML using neural networks with many layers — think of it like an advanced ML technique for cases like image recognition or LLMs (e.g., Claude, GPT), where features are too complex to engineer by hand.

- Neural Network
  - Convolution Neural Network (CNN)
  - Recurrent Neural Network (RNN)
- Transformers
