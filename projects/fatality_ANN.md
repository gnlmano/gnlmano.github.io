## Neural Network Extension (PyTorch)

### 1. Motivation for the Extension
- The original project focused on classical tabular models, where **XGBoost ultimately delivered the best performance**.  
- This extension explores whether a **neural network approach** can capture additional structure in the data, particularly around **high-cardinality diagnosis codes (ICD-9)**.  
- The goal was primarily **methodological**: demonstrate a full deep-learning workflow on tabular clinical data rather than replace the tree-based solution.

---

### 2. Neural Network Architecture
A simple feed-forward neural network was implemented in **PyTorch**.

Key design choices:
- **Embedding layer** replaces one-hot encoding for ICD-9 codes.
- Dense tabular features are concatenated with the learned diagnosis embedding.
- Dropout was used to reduce overfitting.

The model outputs **two logits**, trained using **CrossEntropyLoss**.

---

### 3. Handling High-Cardinality ICD-9 Codes
ICD-9 diagnosis codes contain hundreds of possible categories.

Instead of one-hot encoding (which would produce a very sparse feature space), the model learns **dense embeddings** for diagnosis codes.

Implementation details:
- A vocabulary was created **only from the training set** to avoid leakage.
- Each ICD-9 code was mapped to an integer index.
- A reserved **UNK index** handles unseen codes in validation or test data.
- The embedding layer learns a compact representation of diagnoses during training.

This allows the network to capture **similarities between diagnoses** that one-hot encoding cannot.

---

### 4. Training Pipeline
The neural network was trained using a standard deep learning workflow:

- **Mini-batch training** via PyTorch `DataLoader`
- **Adam optimizer**
- **CrossEntropyLoss**
- **Early stopping based on validation ROC-AUC**

The training loop tracked:

- training loss
- validation loss
- validation ROC-AUC

Early stopping was applied to prevent overfitting and select the best model checkpoint.

---

### 5. Hyperparameter Search
A small **manual grid search** was implemented using `itertools.product`.

Parameters explored:

- learning rate
- embedding dimension
- hidden layer sizes
- dropout rate
- weight decay

Each configuration was trained with early stopping, and the **best validation ROC-AUC** was recorded.

The goal was not exhaustive optimization but to demonstrate **systematic tuning of neural network hyperparameters**.

---

### 6. Results
The neural network achieved significantly lower performance than the gradient boosting model used in the main project.

| Model | Test Accuracy |
|------|-------------|
| XGBoost | **0.93** |
| Neural Network (Embeddings) | **~0.65** |

This outcome is consistent with typical behavior on **structured tabular datasets**, where tree-based models often outperform neural networks unless very large datasets are available.

---

### 7. Key Learning Points
This extension demonstrates several important deep learning practices:

- implementing neural networks with **PyTorch**
- using **embedding layers for high-cardinality categorical features**
- building reusable **training pipelines**
- applying **early stopping for model selection**
- performing **manual hyperparameter tuning**
- integrating neural networks into a broader ML workflow

While the neural network did not outperform XGBoost, the exercise highlights how deep learning techniques can be adapted to **structured clinical datasets** and how their performance compares to specialized tabular models.

---

**[View the full project on GitHub](https://github.com/gnlmano/Probability-of-Death-ANN-Extension?tab=readme-ov-file)**  
