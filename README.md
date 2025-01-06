# Radar Gesture Recognition

# Hand Gesture Recognition Using FMCW Radar

## Problem
Most FMCW hand gesture recognition systems rely heavily on 2D data processing, such as range-doppler images, which require high power consumption, memory, and compute. My proposal is to build a lightweight radar processing algorithm that requires fewer computational resources than traditional approaches. 

This project extends the research paper “[Gesture Recognition for FMCW Radar on the Edge](https://arxiv.org/pdf/2310.08876),” where I introduce environmental and user behavior impact on hand gesture detection and performance. I will also explore whether feature vectors, such as velocity and range, exhibit distinct patterns corresponding to individuals performing gestures. This analysis may reveal unique, individual-specific gesture execution patterns, suggesting that each person has a recognizable gesture profile. Additionally, I will investigate whether gesture classification can be effectively achieved using simpler, more interpretable algorithms, such as decision trees, instead of the recurrent neural networks proposed in the reference paper.

## DataSet
The dataset is downloaded from the following website: [IEEE DataPort](https://ieee-dataport.org/documents/60-ghz-fmcw-radar-gesture-dataset).

The dataset was collected using the BGT60TR13C XENSIV™ 60 GHz frequency-modulated continuous wave radar. A total of 8 individuals performed 5 different hand gestures:
- **SwipeUP**
- **SwipeDown**
- **SwipeLeft**
- **SwipeRight**
- **Push** (toward the sensor)

These gestures were performed in various environments, including a gym, library, kitchen, bedroom, shared office room, and closed meeting room. The dataset contains 21,000 NumPy files, each a 4-dimensional array: `(frame #, antenna #, chirp #, sample #)`.

## Modelling Approach

### Method 1: Range-Doppler Feature Approach
1. Raw data signals were processed into range-doppler profiles using signal processing techniques and preprocessing functions.
2. A target variable was created based on the gesture extracted from the file name.
3. Data was split into training, validation, and test datasets.
4. A deep learning model was applied:
   - Time-distributed CNN layer with 32 filters, kernel size (3,3), and max pooling (2,2) to extract spatial features.
   - LSTM layer to extract temporal features.
   - Fully connected layer with softmax activation function for classifying the 5 hand gestures.

---

### Method 2: Light Feature Extraction Approach
1. Extracted lightweight features such as:
   - **Range**, **Doppler**, **Signal Magnitude**, **Horizontal**, and **Vertical Angles**.
2. Metadata (gesture name, environment label, user label) was extracted from file names.
3. Labels were one-hot encoded, and the dataset was standardized.
4. Data was split into training, validation, and test datasets.
5. Gradient Boosting was used for classification with three configurations:
   1. **Radar features only**.
   2. **Radar features + Gesture performer**.
   3. **Radar features + Gesture performer + Environment variable**.

---

## Results

### Method 1: Accuracy and Confusion Matrix
- Achieved **~99% accuracy** and **<0.1 loss**.
- Training and validation datasets showed similar performance, indicating no overfitting.
- **Advantages**:
  - Range-Doppler profiles provide both distance and velocity information for accurate gesture dynamics analysis.
  - Deep learning models process this data directly for gesture recognition, minimizing post-processing needs.
- **Challenges**:
  - Range-Doppler profiles are large, making them computationally expensive to store and process without optimization.

---

### Method 2: Accuracy and Confusion Matrix
- Gradient Boosting classifier with grid search for optimal parameter tuning achieved:
  - **88% accuracy** using radar-based features only.
  - **91% accuracy** using radar-based features + user info.
  - **92% accuracy** using radar-based features + user info + environment variable.

---

### Explainable Model: Decision Trees
- Achieved **0.87 accuracy** with a simple decision tree model.
- Used SHAP to explain model decisions:
  - Example: For the data point at index `20244`, **Doppler** positively influenced the classification score.
  - In contrast, for indices `7341` and `12429`, **Doppler** negatively contributed to the score.
- Demonstrates how individual features influence predictions differently, enhancing interpretability.

---

## Conclusion
The deep learning model applied to range-doppler images achieves high accuracy but faces deployment challenges on low-compute, low-memory embedded IoT devices. To address this, I adopted the feature extraction algorithm proposed in the referenced paper. This approach:
1. Extracted lightweight features across multiple frames (Doppler, range, signal magnitude, horizontal, and vertical angles).
2. Avoided RNNs by averaging features across frames and using faster classification algorithms like Gradient Boosting and Decision Trees.

This method maintained computational efficiency while improving performance:
- Metadata-derived features, despite lower feature importance, improved Gradient Boosting model accuracy from **88% to 92%**.

