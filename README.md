# Apriori

## AIM

To implement the Apriori Algorithm in Python using the dataset for identifying frequent itemsets and generating strong association rules based on minimum support and confidence thresholds.

## ALGORITHM

### Step 1: Import Libraries

Import pandas for data handling.

Import combinations from itertools to generate item combinations.

### Step 2: Load the Dataset

Read market.csv using pandas.

Convert each row’s items into a list to form a list of transactions.

### Step 3: Define Minimum Thresholds

Set minimum support = 0.30

Set minimum confidence = 0.60

###b Step 4: Generate the List of All Unique Items

Extract all unique items from all transactions.

### Step 5: Calculate Support

Define a function calculate_support(itemset)
→ Computes support = count(itemset appears) / total transactions

### Step 6: Apriori Algorithm – Generate Frequent Itemsets

Start with 1-itemsets.

Calculate support for each.

Keep only those meeting support ≥ 0.3.

Generate k+1 itemsets by joining frequent k-itemsets.

Repeat until no more itemsets can be formed.

### Step 7: Generate Association Rules

For each frequent itemset of size ≥2:

Split itemset into antecedent & consequent.

Calculate support, confidence, and lift.

Keep rules with confidence ≥ 0.60.

### Step 8: Identify Strongest Rule

Among all generated rules, the rule with highest lift is considered the strongest.

### Step 9: Display Results

Print frequent itemsets with support.

Print association rules with support, confidence, and lift.

Print strongest rule and conclusion.


###Dataset
###Transaction_ID,Items
### 1,Milk,Bread,Butter
### 2,Milk,Bread,Jam
### 3,Bread,Butter,Cheese
### 4,Milk,Bread,Butter,Eggs
### 5,Bread,Butter,Cheese,Eggs
### 6,Milk,Bread,Eggs
### 7,Bread,Butter
### 8,Milk,Bread,Butter,Jam
### 9,Milk,Eggs
### 10,Bread,Butter,Jam
### 11,Milk,Bread,Butter,Cheese
### 12,Milk,Bread,Jam,Eggs
### 13,Bread,Butter,Cheese
### 14,Milk,Bread,Butter,Eggs
### 15,Milk,Bread,Butter,Jam
### 16,Bread,Butter,Cheese,Eggs
### 17,Milk,Bread,Butter
### 18,Milk,Bread,Butter,Cheese
### 19,Milk,Bread,Butter,Jam
### 20,Bread,Butter,Eggs

```Python
import pandas as pd
from itertools import combinations
```

### Step 1: Load Dataset
```Python
df = pd.read_csv('market.csv')
transactions = df['Items'].apply(lambda x: x.split(','))

print("Sample Transactions:")
for i, t in enumerate(transactions[:5], 1):
    print(f"Transaction {i}: {t}")
```
### Step 2: Define Minimum Support and Confidence
```Python
min_support = 0.3   # 30%
min_confidence = 0.6
```
### Step 3: Generate All Possible Items
```Python
all_items = sorted({item for trans in transactions for item in trans})
```
### Step 4: Function to Calculate Support
```Python
def calculate_support(itemset):
    count = sum(1 for trans in transactions if itemset.issubset(set(trans)))
    return count / len(transactions)
```
### Step 5: Generate Frequent Itemsets
```Python
def apriori(transactions, min_support):
    freq_itemsets = []
    k = 1
    current_itemsets = [frozenset([item]) for item in all_items]

    while current_itemsets:
        valid_itemsets = []
        for itemset in current_itemsets:
            support = calculate_support(itemset)
            if support >= min_support:
                valid_itemsets.append((itemset, support))
                print(f"Frequent Itemset: {set(itemset)}, Support: {round(support,2)}")
        freq_itemsets.extend(valid_itemsets)

        # Generate next-level candidates
        next_items = []
        for i in range(len(valid_itemsets)):
            for j in range(i+1, len(valid_itemsets)):
                union = valid_itemsets[i][0] | valid_itemsets[j][0]
                if len(union) == k + 1 and union not in next_items:
                    next_items.append(union)
        current_itemsets = next_items
        k += 1

    return freq_itemsets
```

### Step 6: Run Apriori
```Python
print("\nFrequent Itemsets (Support ≥ 0.3):")
frequent_itemsets = apriori(transactions, min_support)
```
### Step 7: Generate Association Rules
```Python
print("\n Association Rules (Confidence ≥ 0.6):")
rules = []
for itemset, support in frequent_itemsets:
    if len(itemset) > 1:
        for i in range(1, len(itemset)):
            for antecedent in combinations(itemset, i):
                antecedent = set(antecedent)
                consequent = itemset - antecedent
                if consequent:
                    support_X = calculate_support(antecedent)
                    support_XY = calculate_support(itemset)
                    confidence = support_XY / support_X
                    lift = confidence / calculate_support(consequent)
                    if confidence >= min_confidence:
                        rules.append((antecedent, consequent, support_XY, confidence, lift))
                        print(f"Rule: {antecedent} → {consequent} | "
                              f"Support: {round(support_XY,2)} | "
                              f"Confidence: {round(confidence,2)} | "
                              f"Lift: {round(lift,2)}")
```
### Step 8: Identify the Strongest Rule
```Python
if rules:
    strongest_rule = max(rules, key=lambda x: x[4])  # highest lift
    print("\nStrongest Association Rule:")
    print(f"{strongest_rule[0]} → {strongest_rule[1]}")
    print(f"Support: {round(strongest_rule[2],2)}, "
          f"Confidence: {round(strongest_rule[3],2)}, "
          f"Lift: {round(strongest_rule[4],2)}")
```
```Python
    print(f"The pair {strongest_rule[0]} → {strongest_rule[1]} is the strongest association rule.")
    print("It indicates that when customers buy", list(strongest_rule[0])[0],
          "they are highly likely to buy", list(strongest_rule[1])[0], "together.")
else:
    print("\nNo strong rules found with the given thresholds.")
```

### Output:

<img width="922" height="137" alt="image" src="https://github.com/user-attachments/assets/841bef93-0559-4d7f-85b1-2f1cad46818c" />

<img width="1005" height="482" alt="image" src="https://github.com/user-attachments/assets/d2b07545-d704-4b3e-abf2-20d153af6808" />

<img width="1137" height="852" alt="image" src="https://github.com/user-attachments/assets/4418ac86-eee9-42c6-97e9-e8bd3e80894a" />

<img width="874" height="437" alt="image" src="https://github.com/user-attachments/assets/7efc2f1d-a620-4170-8e18-794cbe8c78cc" />

### Result:

the Apriori Algorithm in Python using the dataset for identifying frequent itemsets and generating strong association rules based on minimum support and confidence thresholds implemented sucessfully.


