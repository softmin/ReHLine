# ReHLine: Efficient Solver for ERM with PLQ Loss and Linear Constraints <a href="https://github.com/softmin/ReHLine"><img src="images/logo.png" align="right" height="138" /></a>

[![PyPI version](https://badge.fury.io/py/rehline.svg)](https://badge.fury.io/py/rehline)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Documentation](https://img.shields.io/badge/docs-latest-blue.svg)](https://rehline-python.readthedocs.io)
[![Paper](https://img.shields.io/badge/NeurIPS-2023-red.svg)](https://openreview.net/pdf?id=3pEBW2UPAD)

> **Fast, scalable, and flexible optimization for machine learning**

**ReHLine** is a powerful solver for large-scale **empirical risk minimization (ERM) problems** with **convex piecewise linear-quadratic (PLQ) loss functions** and **linear constraints**. Whether you're training SVMs, quantile regression models, or Huber-regularized estimators, ReHLine delivers exceptional performance with provable convergence guarantees.

### � Use Cases

ReHLine excels at solving a wide range of machine learning problems, following are some examples:

| **Problem** | **Description** | **Applications** |
|------------|-----------------|------------------|
| **Support Vector Machines (SVM)** | Binary and multi-class classification with hinge loss | Text classification, image recognition |
| **Fair SVM** | SVM with fairness constraints for equitable predictions | Bias-aware hiring, lending decisions |
| **Quantile Regression** | Estimate conditional quantiles with check loss | Risk assessment, forecasting |
| **Huber Regression** | Robust regression resistant to outliers | Noisy data analysis, anomaly detection |
| **Elastic Net** | Combined L1/L2 regularization for feature selection | High-dimensional genomics, finance |

> **💡 Beyond Pre-built Losses and Constraints**: ReHLine is a **general-purpose solver** that can optimize **any convex piecewise linear-quadratic (PLQ) loss** with **arbitrary linear constraints**. The examples above are just common use cases—you can define custom loss functions and constraints for your specific problem. See detailed documentation for each interface: [Python](https://rehline-python.readthedocs.io) | [R](https://github.com/softmin/ReHLine-r) | [C++](https://github.com/softmin/ReHLine-cpp)

## ✨ Why ReHLine?

- **🚀 Blazing Fast**: Linear computational complexity per iteration scales efficiently to millions of samples
- **🎯 Versatile**: Supports any convex piecewise linear-quadratic loss (hinge, check, Huber, and more)
- **🔒 Constrained Optimization**: Handle linear equality and inequality constraints with ease
- **📊 Scikit-Learn Compatible**: Drop-in replacement for standard ML workflows. Seamlessly integrates with `GridSearchCV`, `Pipeline`, and all scikit-learn tools


## 📦 Available Interfaces and Installation

ReHLine provides native implementations across multiple platforms:

| **Interface** | **Repository** | **Installation** |
|--------------|----------------|------------------|
| 🐍 **Python** | [ReHLine-python](https://github.com/softmin/ReHLine-python) | `pip install rehline` |
| 📊 **R** | [ReHLine-r](https://github.com/softmin/ReHLine-r) | `install.packages("rehline")` |
| ⚡ **C++** | [ReHLine-cpp](https://github.com/softmin/ReHLine-cpp) | Build from source |

All interfaces leverage the same highly optimized C++ core for maximum performance.

## 🚀 Quick Start with Python

```python
## Example: SVM Classification with fairness constraint
import numpy as np
from sklearn.datasets import make_classification
from sklearn.model_selection import train_test_split
from rehline import plq_Ridge_Classifier

# generate the dataset
X, y = make_classification(n_samples=2000, n_classes=2)

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.25, stratify=y, random_state=42)


fclf = plq_Ridge_Classifier(C=1.0,
                          loss={'name': 'svm'},
                          constraint=[{'name': 'fair',
                                        'sen_idx': [0], ## column index of sensitive feature
                                        'tol_sen': 0.1, ## tolerance for fairness constraint
                                        }],
                          max_iter=50000)
fclf.fit(X, y)

# Make predictions
y_pred = fclf.predict(X)
```



## ⚡ Performance Benchmarks

ReHLine delivers **exceptional speed** compared to state-of-the-art solvers. Here are speed-up factors on real-world datasets:

### Speed Comparison vs. Popular Solvers

| **Task** | **vs. ECOS** | **vs. MOSEK** | **vs. SCS** | **vs. Specialized Solvers** |
|----------|--------------|---------------|-------------|----------------------------|
| **SVM** | **415×** faster | **∞** (failed) | **340×** faster | **4.5×** vs. LIBLINEAR |
| **Fair SVM** | **273×** faster | **100×** faster | **252×** faster | **∞** vs. DCCP (failed) |
| **Quantile Regression** | **2843×** faster | **∞** (failed) | **∞** (failed) | — |
| **Huber Regression** | **∞** (failed) | **452×** faster | **∞** (failed) | **2.4×** vs. hqreg |
| **Smoothed SVM** | — | — | — | **1.6-2.3×** vs. SAGA/SAG/SDCA/SVRG |

> **Note**: "∞" indicates the competing solver failed to produce a valid solution or exceeded time limits. Results from [NeurIPS 2023 paper](https://openreview.net/pdf?id=3pEBW2UPAD).

### Detailed Benchmarks

All benchmarks are reproducible via [benchopt](https://github.com/benchopt/benchopt) at our [ReHLine-benchmark](https://github.com/softmin/ReHLine-benchmark) repository.

| **Problem** | **Benchmark Code** | **Interactive Results** |
|------------|-------------------|------------------------|
| SVM | [Code](https://github.com/softmin/ReHLine-benchmark/tree/main/benchmark_SVM) | [📊 View](https://rehline-python.readthedocs.io/en/latest/_static/benchmark/benchmark_SVM.html) |
| Smoothed SVM | [Code](https://github.com/softmin/ReHLine-benchmark/tree/main/benchmark_sSVM) | [📊 View](https://rehline-python.readthedocs.io/en/latest/_static/benchmark/benchmark_sSVM.html) |
| Fair SVM | [Code](https://github.com/softmin/ReHLine-benchmark/tree/main/benchmark_FairSVM) | [📊 View](https://rehline-python.readthedocs.io/en/latest/_static/benchmark/benchmark_FairSVM.html) |
| Quantile Regression | [Code](https://github.com/softmin/ReHLine-benchmark/tree/main/benchmark_QR) | [📊 View](https://rehline-python.readthedocs.io/en/latest/_static/benchmark/benchmark_QR.html) |
| Huber Regression | [Code](https://github.com/softmin/ReHLine-benchmark/tree/main/benchmark_Huber) | [📊 View](https://rehline-python.readthedocs.io/en/latest/_static/benchmark/benchmark_Huber.html) |

## 📚 Citation

If you use ReHLine in your research, please cite our NeurIPS 2023 paper:

```bibtex
@inproceedings{dai2023rehline,
  title={ReHLine: Regularized Composite ReLU-ReHU Loss Minimization with Linear Computation and Linear Convergence},
  author={Dai, Ben and Qiu, Yixuan},
  booktitle={Thirty-seventh Conference on Neural Information Processing Systems},
  year={2023}
}
```

## 🔗 ReHLine Ecosystem

<table>
<tr>
<td width="50%">

### 🏠 Core Projects
- **[ReHLine](https://github.com/softmin/ReHLine)** - Main repository and documentation
- **[ReHLine-python](https://github.com/softmin/ReHLine-python)** - Python interface (this repo)
- **[ReHLine-r](https://github.com/softmin/ReHLine-r)** - R interface
- **[ReHLine-cpp](https://github.com/softmin/ReHLine-cpp)** - High-performance C++ core

</td>
<td width="50%">

### 📊 Resources
- **[ReHLine-benchmark](https://github.com/softmin/ReHLine-benchmark)** - Reproducible benchmarks
- **[Project Homepage](https://rehline.github.io)** - Official website
- **[Documentation](https://rehline-python.readthedocs.io)** - Full Python docs
- **[NeurIPS 2023 Paper](https://openreview.net/pdf?id=3pEBW2UPAD)** - Research paper

</td>
</tr>
</table>
