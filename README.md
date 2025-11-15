# AI_ML_Lab_Exam_24UG00303
Data Exploration lab exam for my AI ML course. 

The project pipeline covers the entire data science workflow:
* **Data Integration:** Merging three separate and messy data sources (`.csv` and `.json`).
* **Data Cleaning:** Handling missing values, standardizing text, and coalescing columns.
* **Feature Engineering:** Creating new, insightful features from existing data.
* **Exploratory Data Analysis (EDA):** Visualizing data patterns and relationships.
* **Statistical Analysis:** Identifying class imbalance, low-variance features, and multicollinearity.
* **Modeling & Evaluation:** Training and evaluating a Random Forest Classifier.
* **Model Comparison:** Training a k-Nearest Neighbors (k-NN) model to compare performance.
* **Final Analysis:** Synthesizing all findings to draw conclusions and recommend a final model.

---

## 2. Data Sources

This project integrated data from three distinct files:
* `zoo.csv`: Contains the core biological features for 101 animals (e.g., `hair`, `feathers`, `eggs`, `milk`).
* `class.csv`: A mapping file that links `class_type` numbers to their proper names (e.g., "Mammal", "Bird").
* `auxiliary_metadata.json`: A messy JSON file containing supplementary data like conservation status, diet, and habitat, often in inconsistent formats.

---

## 3. Project Workflow & Methodology

### Step 1: Data Integration and Cleaning
The first challenge was to combine the three disparate data sources into a single, clean DataFrame. This involved:
* Merging the three files on common keys (`animal_name` and `class_type`).
* **Coalescing Columns:** Combining messy metadata columns (e.g., `habitat` and `habitats` were merged into a single `habitat_type` column).
* **Data Standardization:** Cleaning text values to be consistent (e.g., "omnivor" was corrected to "omnivore").
* **Handling NaNs:** Filling missing categorical data with the string "unknown" and missing numerical data with the median value to ensure the dataset was complete.

### Step 2: Feature Engineering
Two new features were engineered from the integrated dataset to potentially improve model performance:
* `is_endangered`: A binary flag (1 or 0) derived from the `conservation_status` column.
* `diet_complexity`: A numerical feature (0-3) mapped from the `diet` column (e.g., 'herbivore': 1, 'omnivore': 2, 'carnivore': 3).

The final, engineered dataset was saved as `zoo_engineered_dataset.csv`.

### Step 3: Exploratory & Statistical Analysis
Before modeling, a deep dive into the data revealed key insights:
* **Class Imbalance:** The dataset is highly imbalanced (10.25 ratio), with **Mammals (Class 1)** being over 10 times more common than **Amphibians (Class 7)**. This posed a risk of model bias.
* **High Correlation:** Features like `milk`, `hair`, and `eggs` are very strongly correlated, which makes perfect biological sense (e.g., animals with `milk` almost never lay `eggs`). These features were expected to be powerful predictors.
* **Low Variance:** Features like `venomous` and the new `is_endangered` were found to be "near-constant" (very low variance), suggesting they would be weak predictors.
* **Visualization:**
    * A **Pairplot** of `milk`, `feathers`, and `fins` showed clear, distinct clusters for different animal classes.
    * A **Heatmap** of `habitat_type` vs. `Class_Type` revealed strong patterns (e.g., Class 4/Fish is 100% in 'water').
    * A **Stacked Bar Chart** showed that most animals are of 'least concern' regarding conservation.

### Step 4: Modeling and Evaluation
The data was split (70% train, 30% test) using stratification to handle the class imbalance. Two models were trained and evaluated.

#### Model 1: Random Forest Classifier
* **Parameters:** `n_estimators=120`, `max_depth=12`, `min_samples_split=4`
* **Performance:**
    * **Test Accuracy: 96.8%**
    * Training Accuracy: 100.0%
    * Overfitting Gap: 3.2%
* **Analysis:** The model performed extremely well but clearly "memorized" the training data, leading to a small overfitting gap.

#### Model 2: k-Nearest Neighbors (k-NN)
* **Parameters:** `n_neighbors=5`
* **Preprocessing:** Features were scaled using `StandardScaler` (a requirement for k-NN).
* **Performance:**
    * **Test Accuracy: 96.8%**
    * Training Accuracy: 97.1%
    * Overfitting Gap: 0.4%
* **Analysis:** The k-NN model achieved the *exact same* high accuracy as the Random Forest but generalized much better, with almost no overfitting.

---

## 4. Key Results & Final Analysis

### Model Comparison
| Model | Test Accuracy | Training Accuracy | Overfitting Gap |
|---|---|---|---|
| Random Forest | **96.8%** | 100.0% | 3.2% |
| k-Nearest Neighbors | **96.8%** | 97.1% | **0.4%** |

Both models were excellent, but the **k-NN model is the recommended choice** as it provides identical performance with less complexity and significantly less overfitting.

### Key Predictive Features (from Random Forest)
The model's decisions were driven by fundamental biological definitions, confirming the high quality of the data.
1.  **`eggs`** (Importance: 0.139)
2.  **`milk`** (Importance: 0.119)
3.  **`feathers`** (Importance: 0.117)
4.  **`toothed`** (Importance: 0.093)
5.  **`hair`** (Importance: 0.089)

The engineered features (`is_endangered` and `diet_complexity`) had near-zero importance, as the original 16 features were already so predictive.

### Model Weakness
Both models made the exact same (and only) error on the test set:
* **Misclassification:** One instance of **Class 3 (Reptile)** was incorrectly predicted as **Class 4 (Fish)**.
* **Reason:** This is a data problem, not a model fault. Class 3 was the most under-represented class in the test set (only 2 samples), making it difficult to learn its patterns robustly.

## 5. Conclusion & Recommendations

1.  **High-Quality Data:** The dataset is exceptionally clean and well-featured. The 16 core biological features are so significant that a 97% test accuracy is achievable with simple models.
2.  **Model Choice:** The **k-NN (k=5) model** is recommended. It is simple, robust, and provides top-tier accuracy without overfitting.
3.  **Area for Improvement:** The only weakness is the Reptile/Fish confusion. The best way to fix this would be to **gather more data samples for Class 3 (Reptiles)**, as it is the most under-represented class in the dataset.
