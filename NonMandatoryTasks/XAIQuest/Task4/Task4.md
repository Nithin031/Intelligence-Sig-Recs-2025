# CNN Model Robustness Evaluation

## Robustness Evaluation on CIFAR-10

This task solution evaluates the robustness of Custom CNN model  under various input perturbations and interventions.

### Data Corruption Experiments

I evaluated models under several common corruption types at different severity levels. The following results summarize the accuracy observed:

| Corruption Type | Severity / Level | Accuracy |
| --------------- | ---------------- | -------- |
| Gaussian Noise  | 5                | 0.8909   |
| Gaussian Noise  | 20               | 0.5348   |
| Gaussian Noise  | 40               | 0.2120   |
| Poisson Noise   | 0.25             | 0.5134   |
| Poisson Noise   | 1.0              | 0.7703   |
| Poisson Noise   | 4.0              | 0.8831   |
| Gaussian Blur   | 1                | 0.5768   |
| Gaussian Blur   | 3                | 0.1599   |
| Gaussian Blur   | 6                | 0.1597   |
| Motion Blur     | 3                | 0.8058   |
| Motion Blur     | 10               | 0.2707   |
| Motion Blur     | 20               | 0.1825   |
| Color Shift     | (5, 0.05)        | 0.9081   |
| Color Shift     | (20, 0.2)        | 0.8588   |
| Color Shift     | (40, 0.4)        | 0.6096   |

### Input Interventions

I applied three types of test-time interventions on the images and measured model performance:

| Intervention | Accuracy |
| ------------ | -------- |
| Patch        | 0.8265   |
| Occlude      | 0.68     |
| Shuffle      | 0.8225   |

**Notes:**

* **Patch:** Inserted a small colored pixel (size 5×5) in the top-left corner (0,0) for images of a target class; color red (RGB normalized: 1.0,0,0).
* **Occlude:** Portions of object regions masked to simulate missing information.
* **Shuffle:** Randomly shuffled color channels to disrupt color information.

### Mixup / CutMix Augmentation Comparison

I trained additional versions of the models with Mixup and CutMix augmentations to evaluate their effect on robustness:

* **Mixup:** Combines pairs of images and their labels linearly. Provides smoother decision boundaries.
* **CutMix:** Replaces random patches in an image with patches from another image and adjusts labels proportionally.

**Observations:**

* Models trained with Mixup/CutMix show improved robustness to noise and minor interventions.
* The accuracy under patch, occlusion, and shuffle interventions was slightly higher (~0.83–0.85) compared to models trained on clean data.
* Feature maps indicate that Mixup/CutMix models rely more evenly on distributed object features rather than overfitting to localized color or texture cues.

---

## Feature Map Observations

We visualized intermediate feature maps at different convolutional layers to understand how the network processes images under stress:

* **Early layers:** Feature maps closely resemble the input image, capturing edges, textures, and basic shapes.
* **Middle layers:** Representations abstract object parts; some similarity to the original image remains.
* **Deep layers:** Feature maps become highly abstract and pixelated, encoding high-level features rather than image-level details.

---

## Intervention Analysis

### Patch Insertion

* Location: Top-left corner (0,0)  
* Size: 15×15 pixels  
* Color: (RGB: 69,69.69 )  
* Accuracy: 0.8265  
* Interpretation: The model can latch onto localized cues but is not fully dominated by the patch.

### Occlusion

* Certain object regions masked (set to zero)  
* Accuracy: 0.68  
* Interpretation: Occluding key object parts significantly reduces performance, highlighting sensitivity to spatial completeness.

### Color Channel Shuffle

* Randomly shuffles RGB channels  
* Accuracy: 0.8225  
* Interpretation: While color contributes to predictions, the model primarily relies on shape and edge features.

---

## Metrics Used

* **Overall Accuracy:** Fraction of correctly predicted labels across the test set.  
* **Classification Report:** Precision, recall, F1-score per class.  
* **Confusion Matrix:** Visualized misclassifications to understand error patterns.

---

## Summary

* Models trained on clean CIFAR-10 exhibit robustness to low-level noise but fail under high severity corruptions.  
* Patch and shuffle interventions indicate reliance on color patterns for classification of certain classes.  
* Occluding critical object regions has the strongest impact on model performance.  
* Feature map analysis confirms the progressive abstraction from edges/textures to high-level semantic features.  
* Mixup and CutMix augmentations improve robustness by encouraging the network to distribute attention across the object rather than focusing on small local cues.
