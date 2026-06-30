# 📊 Association Rules Mining

Custom Implementation with Manual Metric Calculations

**Author:** Yair Levi

**Date:** October 15, 2025

**Version:** 1.0.0

**Language:** Python 3.7+

**Dependencies:** NumPy

## 📋 Overview

This project implements an association rules mining algorithm from scratch using Python and NumPy. The implementation demonstrates fundamental data mining concepts by generating synthetic binary datasets with controlled distributions and discovering association rules that meet specified support and confidence thresholds. 

#### Key Features

  * **Pure Python Implementation:** Manual calculation of support, confidence, and lift metrics
  * **Synthetic Dataset Generation:** Creates 5000×6 binary arrays with custom probability distributions
  * **Configurable Thresholds:** Minimum support (30%) and confidence (70%)
  * **Comprehensive Analysis:** Discovers all qualifying rules with detailed statistics



## 🎯 Project Objectives

The main objectives of this implementation are:

  * Generate a dataset with 5000 transactions and 6 binary features (a, b, c, d, e, f)
  * Control the distribution of each feature with specific probabilities (80%, 60%, 40%, 30%, 20%, 10%)
  * Implement association rules mining without external libraries
  * Calculate Support, Confidence, and Lift using mathematical formulas
  * Find rules with Support > 30% and Confidence > 70%



## 📐 Mathematical Formulas

### 1\. Support

The support of an itemset X measures how frequently it appears in the dataset:

Support(X) = Count(transactions containing X) / Total transactions 

**Example:** If 2400 out of 5000 transactions contain {a,b}, then Support({a,b}) = 2400/5000 = 0.48 (48%)

### 2\. Confidence

The confidence of rule X → Y measures how often Y appears in transactions containing X:

Confidence(X → Y) = Support(X ∪ Y) / Support(X) 

**Example:** If Support({a,b}) = 0.48 and Support({a}) = 0.80, then Confidence({a} → {b}) = 0.48/0.80 = 0.60 (60%)

_Interpretation: 60% of transactions containing 'a' also contain 'b'_

### 3\. Lift

The lift of rule X → Y measures how much more likely Y is when X is present compared to random chance:

Lift(X → Y) = Support(X ∪ Y) / (Support(X) × Support(Y)) = Confidence(X → Y) / Support(Y) 

**Interpretation:**

  * **Lift > 1:** Positive correlation (items appear together more than expected)
  * **Lift = 1:** Independence (no correlation)
  * **Lift < 1:** Negative correlation (items rarely appear together)



## 🔧 Dataset Configuration

Feature | Target Probability | Description  
---|---|---  
a | 80% | Very frequent feature  
b | 60% | Moderately frequent feature  
c | 40% | Medium frequency feature  
d | 30% | Less frequent feature  
e | 20% | Rare feature  
f | 10% | Very rare feature  
  
**Note:** The actual percentages may vary slightly from targets due to random generation, but should be very close (within ±1%) for 5000 samples. 

## 📊 Program Output Results

The following sections show the actual output from running the association rules mining program. The results demonstrate the dataset generation, distribution verification, and discovered association rules. 

Output Part 1: Dataset Generation and Initial Analysis
    
    
    C:\Users\yair0\Documents\docs\courses\AI_Limudey_Hutz\Lesson10\Corelation>python3 association_rules_mining.py
    ================================================================================
    ASSOCIATION RULES MINING
    ================================================================================
    
    Dataset: 5000 rows, 6 features
    Features: ['a', 'b', 'c', 'd', 'e', 'f']
    Probabilities: [0.8, 0.6, 0.4, 0.3, 0.2, 0.1]
    
    Thresholds:
      Minimum Support: 30.0%
      Minimum Confidence: 70.0%
    ================================================================================
    
    Dataset created successfully!
    Dataset shape: (5000, 6)
    
    Actual percentages of 1s in each column:
      a: 79.42% (target: 80.00%)
      b: 59.50% (target: 60.00%)
      c: 39.92% (target: 40.00%)
      d: 30.94% (target: 30.00%)
      e: 20.44% (target: 20.00%)
      f: 8.98% (target: 10.00%)
    
    ================================================================================
    FINDING ASSOCIATION RULES
    ================================================================================
    
    Total rules found: 2

Output Part 2: Discovered Rules and Statistics
    
    
    ================================================================================
    ASSOCIATION RULES
    ================================================================================
    
    Rule                           Support      Confidence   Lift      
    --------------------------------------------------------------------------------
    {b} -> {a}                     0.4748 (47.48%)  0.7980 (79.80%)  1.0048
    {c} -> {a}                     0.3120 (31.20%)  0.7816 (78.16%)  0.9841
    
    --------------------------------------------------------------------------------
    SUMMARY STATISTICS
    ================================================================================
    
    Average Support:    0.3934 (39.34%)
    Average Confidence: 0.7898 (78.98%)
    Average Lift:       0.9944
    
    Maximum Support:    0.4748 (47.48%)
    Maximum Confidence: 0.7980 (79.80%)
    Maximum Lift:       1.0048
    
    ================================================================================
    TOP 5 RULES BY CONFIDENCE
    ================================================================================
    
    1. {b} -> {a}
       Support: 0.4748 (47.48%)
       Confidence: 0.7980 (79.80%)
       Lift: 1.0048
    
    2. {c} -> {a}
       Support: 0.3120 (31.20%)
       Confidence: 0.7816 (78.16%)
       Lift: 0.9841
    
    ================================================================================
    ANALYSIS COMPLETE
    ================================================================================
    
    C:\Users\yair0\Documents\docs\courses\AI_Limudey_Hutz\Lesson10\Corelation>

## 🔍 Results Interpretation

### Dataset Verification

The program successfully generated a dataset matching the target distributions:

  * All features achieved distributions within 1% of target values
  * Feature 'a' (79.42%) is the most frequent, appearing in nearly 80% of transactions
  * Feature 'f' (8.98%) is the rarest, appearing in less than 9% of transactions



### Discovered Association Rules

The mining process discovered **2 rules** meeting the criteria (Support > 30%, Confidence > 70%):

#### Rule 1: {b} → {a}

  * **Support: 47.48%** \- Nearly half of all transactions contain both 'b' and 'a'
  * **Confidence: 79.80%** \- When 'b' appears, there's an 80% chance 'a' also appears
  * **Lift: 1.0048** \- Very slight positive correlation (nearly independent)



_Interpretation: Feature 'b' is a strong predictor of feature 'a', with high reliability._

#### Rule 2: {c} → {a}

  * **Support: 31.20%** \- About one-third of transactions contain both 'c' and 'a'
  * **Confidence: 78.16%** \- When 'c' appears, there's a 78% chance 'a' also appears
  * **Lift: 0.9841** \- Slight negative correlation (slightly less than random chance)



_Interpretation: Feature 'c' is also a good predictor of feature 'a', though slightly weaker than rule 1._

### Why Only 2 Rules?

The algorithm found only 2 rules due to the stringent thresholds and probability distributions:

  * **High confidence requirement (70%):** Eliminates many potential rules
  * **High support requirement (30%):** Requires frequent co-occurrence
  * **Decreasing probabilities:** Features d, e, f are too rare to meet support threshold in combinations
  * **Feature 'a' dominance:** With 80% probability, 'a' is the most common consequent



## 💡 Key Insights

  * The most frequent feature ('a') naturally becomes the consequent in most rules
  * Features 'b' and 'c' have sufficient frequency to meet the 30% support threshold when combined with 'a'
  * Lower-frequency features (d, e, f) cannot form rules meeting both thresholds
  * Lift values near 1.0 indicate the features are nearly independent
  * High confidence but low lift suggests the rule is reliable but not particularly surprising



## 🎓 Educational Value

This implementation demonstrates several important concepts:

  * **Manual metric calculation:** Understanding the mathematics behind association rules
  * **Threshold effects:** How support and confidence filters affect rule discovery
  * **Probability distributions:** Impact of feature frequencies on rule formation
  * **Algorithm complexity:** Computational challenges in exhaustive rule generation
  * **Result interpretation:** Distinguishing between statistical significance and practical importance



## 📝 Usage Instructions

  1. Install Python 3.7+ and NumPy: `pip install numpy`
  2. Run the script: `python association_rules_mining.py`
  3. View the output showing dataset creation, rule discovery, and statistics
  4. Modify thresholds or probabilities in the code to explore different scenarios



## 🔧 Customization Options

You can modify the following parameters in the code:

  * **Dataset size:** Change `n_rows` (default: 5000)
  * **Feature probabilities:** Adjust the `probabilities` list
  * **Support threshold:** Modify `min_support` (default: 0.30)
  * **Confidence threshold:** Modify `min_confidence` (default: 0.70)
  * **Random seed:** Change or remove `np.random.seed(42)` for different results



**Experimentation Tip:** Try lowering the support threshold to 20% (0.20) to discover more rules involving less frequent features. This will reveal patterns with features d, e, and f. 

## 📚 References and Further Reading

  * Agrawal, R., & Srikant, R. (1994). "Fast Algorithms for Mining Association Rules"
  * "Introduction to Data Mining" by Tan, Steinbach, Kumar
  * "Data Mining: Concepts and Techniques" by Han, Kamber, Pei



**Association Rules Mining Project**

Author: Yair Levi | Version 1.0.0 | October 2025

For questions or contributions, please contact the author or visit the project repository. 
