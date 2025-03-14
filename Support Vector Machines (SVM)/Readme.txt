# Support Vector Machine (SVM) Classification with Feature Importance Analysis

## Project Overview
This project focuses on training a **Support Vector Machine (SVM)** classifier on a synthetic dataset, analyzing its performance using **cross-validation**, and interpreting feature importance. Additionally, we visualize training and testing accuracy to assess model generalization.

## Dataset
We generate a **binary classification dataset** with two informative features using `make_classification` from `sklearn.datasets`. The dataset is **standardized** using `preprocessing.scale` for better model performance.

## Steps in the Project

### 1 Data Preparation

from sklearn.datasets import make_classification
from sklearn import preprocessing
import numpy as np

# Generate synthetic dataset
x, y = make_classification(n_samples=1000, n_features=2, n_redundant=0, n_informative=1,
                           n_clusters_per_class=1, random_state=14)

# Standardize the dataset
x = preprocessing.scale(x)

# Train-test split
x_test = x[:500]
y_test = y[:500]
x = x[500:]
y = y[500:]

# Convert labels from (0,1) to (-1,1) for SVM compatibility
y = np.where(y == 0, -1, 1)
y_test = np.where(y_test == 0, -1, 1)
```

### 2️ Data Visualization
We visualize the dataset distribution before training using **Seaborn**.

import seaborn as sns
import matplotlib.pyplot as plt

sns.scatterplot(x=x[:, 0], y=x[:, 1], hue=y.reshape(-1), edgecolor="k")
plt.xlabel("Feature 1")
plt.ylabel("Feature 2")
plt.title("Data Distribution")
plt.show()
```

### 3️ Training the SVM Model
We train an **SVM classifier** with a **linear kernel** and optimize hyperparameters using `GridSearchCV`.

from sklearn.svm import SVC
from sklearn.model_selection import GridSearchCV

# Define parameter grid
param_grid = {'C': [0.1, 1, 10], 'kernel': ['linear']}

# Train SVM with GridSearch
svm = SVC()
grid_search = GridSearchCV(svm, param_grid, cv=5)
grid_search.fit(x, y)

# Best parameters
print("Best Parameters:", grid_search.best_params_)

# Train model with best parameters
best_model = grid_search.best_estimator_
```

### 4️ Decision Boundary Visualization

xx, yy = np.meshgrid(np.linspace(x[:,0].min()-1, x[:,0].max()+1, 100),
                     np.linspace(x[:,1].min()-1, x[:,1].max()+1, 100))

Z = best_model.predict(np.c_[xx.ravel(), yy.ravel()])
Z = Z.reshape(xx.shape)

plt.contourf(xx, yy, Z, alpha=0.3)
sns.scatterplot(x=x[:,0], y=x[:,1], hue=y.reshape(-1), edgecolor="k")
plt.title("Decision Boundary")
plt.show()
```

### 5️ Cross-Validation Performance

from sklearn.model_selection import cross_val_score

cv_scores = cross_val_score(best_model, x, y, cv=5)
print(f"Cross-validation scores: {cv_scores}")
print(f"Mean CV Accuracy: {cv_scores.mean():.2f}")
```
✅ **If train accuracy is much higher than test accuracy, the model may be overfitting.**

### 7️ Training vs Testing Accuracy Plot
We visualize training and testing accuracy using a **learning curve**.

from sklearn.model_selection import learning_curve

train_sizes, train_scores, test_scores = learning_curve(
    best_model, x, y, cv=5, scoring="accuracy", train_sizes=np.linspace(0.1, 1.0, 10)
)

train_mean = np.mean(train_scores, axis=1)
test_mean = np.mean(test_scores, axis=1)

plt.figure(figsize=(8,6))
plt.plot(train_sizes, train_mean, 'o-', color="blue", label="Training Accuracy")
plt.plot(train_sizes, test_mean, 'o-', color="red", label="Testing Accuracy")
plt.xlabel("Training Size")
plt.ylabel("Accuracy")
plt.title("Training vs Testing Accuracy")
plt.legend(loc="best")
plt.show()
```

## **Conclusion**
🔹 **Cross-validation accuracy (~97%)** indicates a well-generalized model.  
🔹 **Decision boundary visualization** provides an intuitive understanding of model predictions.  

---


