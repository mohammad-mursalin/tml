# Neural Networks Sessional — Complete Theory Guide
### ICE-4206, Pabna University of Science and Technology

This document covers the **theory, equations, and concepts** for all 11 lab exercises. No code — this is for understanding the "why" behind every lab, and for viva preparation.

---

## Lab 1: Perceptron for AND Function (Bipolar)

### Concept

A **perceptron** is the simplest artificial neuron. It takes multiple inputs, computes a weighted sum, and passes the result through a **step (threshold) activation function** to produce a binary decision.

### Architecture

```
   x1 ──w1──┐
             ├──► Σ (net input) ──► f(net) ──► output y
   x2 ──w2──┘
             │
   bias b ───┘
```

### Equations

**Net input:**
$$
y_{in} = \sum_{i=1}^{n} w_i x_i + b = w_1x_1 + w_2x_2 + b
$$

**Bipolar step activation function:**
$$
f(y_{in}) =
\begin{cases}
+1 & \text{if } y_{in} \geq 0 \\
-1 & \text{if } y_{in} < 0
\end{cases}
$$

**Perceptron Learning Rule** (weight update, applied only when prediction is wrong):
$$
w_i(\text{new}) = w_i(\text{old}) + \eta \cdot t \cdot x_i
$$
$$
b(\text{new}) = b(\text{old}) + \eta \cdot t
$$

where:
- $\eta$ = learning rate
- $t$ = target output
- $x_i$ = input value

### Why Bipolar (-1, +1) instead of Binary (0, 1)?

In the update rule, the term $x_i$ directly scales the weight change. If $x_i = 0$ (binary "off" input), **no weight update happens at all** for that connection, even when the prediction is wrong — learning stalls for that input. With bipolar encoding, $x_i = -1$ still actively participates in learning (pushes weights in a meaningful direction).

### Linear Separability

AND is **linearly separable** — a single straight line can separate the (+1) output point from the (-1) points:

```
x2
 1 |  (-1,1)        (1,1)=+1
   |    x             o
   |
   |
-1 |  (-1,-1)       (1,-1)
   |    x              x
   +------------------------ x1
       -1        0        1

  Decision boundary: w1*x1 + w2*x2 + b = 0
```

The perceptron **converges** (weights stop changing) only when a problem is linearly separable. This is why a single perceptron **cannot** learn XOR (Minsky & Papert, 1969) — this historical limitation led to multi-layer networks.

### Convergence

Training stops when a full epoch (pass through all training samples) produces **zero weight updates** — the perceptron correctly classifies every example.

### Key Viva Points
- Perceptron Convergence Theorem: if data is linearly separable, the perceptron rule is *guaranteed* to converge in a finite number of steps.
- Decision boundary equation: $w_1x_1 + w_2x_2 + b = 0$, rearranged to $x_2 = -\frac{w_1x_1+b}{w_2}$ for plotting.

---

## Lab 2: SGD with Delta Learning Rule

### Concept

The **Delta Rule** (Widrow-Hoff Rule) is a generalization of the perceptron rule for **continuous-valued outputs**, minimizing the **squared error** using **gradient descent**. Unlike the perceptron's hard step function, it uses a **linear (identity) activation**.

### Architecture

Same single-layer structure as Lab 1, but:
$$
y = f(y_{in}) = y_{in} \quad \text{(linear/identity activation — no step function)}
$$

### Equations

**Error for a single sample:**
$$
e = t - y
$$

**Loss function (squared error) that we are minimizing:**
$$
E = \frac{1}{2}(t - y)^2
$$

**Delta Rule weight update (derived from gradient descent on E):**
$$
\Delta w_i = \eta \cdot (t - y) \cdot x_i = \eta \cdot e \cdot x_i
$$
$$
w_i(\text{new}) = w_i(\text{old}) + \Delta w_i
$$

**Derivation sketch (why this formula):**
$$
\frac{\partial E}{\partial w_i} = \frac{\partial E}{\partial y}\cdot\frac{\partial y}{\partial w_i} = -(t-y)\cdot x_i
$$
$$
\Delta w_i = -\eta \frac{\partial E}{\partial w_i} = \eta(t-y)x_i
$$

**Mean Squared Error (tracked across all samples, per epoch):**
$$
MSE = \frac{1}{N}\sum_{k=1}^{N}(t_k - y_k)^2
$$

### SGD (Stochastic Gradient Descent)

"Stochastic" = update weights **immediately after every single training sample**, rather than waiting to see the whole dataset.

```
For each epoch:
    For each sample (x, t):      ← one at a time
        compute y
        compute error = t - y
        UPDATE weights immediately
```

### Perceptron Rule vs Delta Rule — Key Difference

| Aspect | Perceptron Rule | Delta Rule |
|---|---|---|
| Activation | Hard step function | Linear (identity) |
| Error type | Binary (right/wrong) | Continuous (t - y) |
| Update trigger | Only on misclassification | Every sample, proportional to error magnitude |
| Convergence guarantee | Only if linearly separable | Converges to least-squares solution regardless |

### Key Viva Points
- Delta rule is literally **gradient descent** on the squared-error loss surface.
- Works even for non-linearly-separable / regression-style problems, since it doesn't require a hard classification decision.

---

## Lab 3: SGD vs Batch Gradient Descent (Delta Rule)

### Concept

Same Delta Rule/error equations as Lab 2. The only difference is **when weights are updated**.

### Comparison Diagram

```
SGD:                                   Batch:
sample1 → update weights               sample1 → compute gradient (store)
sample2 → update weights               sample2 → compute gradient (store)
sample3 → update weights               sample3 → compute gradient (store)
sample4 → update weights               sample4 → compute gradient (store)
   (4 updates per epoch)               → AVERAGE all gradients
                                        → ONE update per epoch
```

### Equations

**SGD update** (applied after each individual sample $k$):
$$
w_i \leftarrow w_i + \eta (t_k - y_k)x_{k,i}
$$

**Batch update** (applied once per epoch, after averaging over all $N$ samples):
$$
w_i \leftarrow w_i + \frac{\eta}{N}\sum_{k=1}^{N}(t_k - y_k)x_{k,i}
$$

### Trade-offs

| Aspect | SGD | Batch |
|---|---|---|
| Updates per epoch | N (one per sample) | 1 |
| Convergence speed (per epoch) | Faster | Slower |
| Path to minimum | Noisy/zig-zag | Smooth |
| Memory/compute per update | Low | Needs full dataset in memory |
| Scalability to huge datasets | Good | Poor (must process everything before any update) |

**Mini-batch Gradient Descent** (middle ground, common follow-up viva topic): average gradients over a small batch (e.g. 16-32 samples) instead of 1 sample or the whole dataset — balances stability and speed.

### Key Viva Points
- Both use the exact same underlying gradient formula; only the **aggregation/timing** of updates differs.
- SGD's noisiness can actually help escape shallow local minima in more complex (non-convex) problems.

---

## Lab 4: Digit Recognition from 5×5 Pixel Images

### Concept

Extending the Delta Rule to a **multi-class, multi-output** problem: recognizing which of 5 digit patterns (5×5 pixel grids) is shown, using a single-layer network with **multiple output neurons**.

### Architecture

```
25 input neurons (flattened 5x5 image)
        │  (fully connected — weight matrix 25 x 5)
        ▼
5 output neurons (one per digit class)
        │
   winner-take-all → predicted digit
```

### Equations

**Flattening:** a 5×5 image $I$ becomes a 25-length vector:
$$
x = [I_{1,1}, I_{1,2}, ..., I_{1,5}, I_{2,1}, ..., I_{5,5}]
$$

**Bipolar conversion:** pixel value 0 → -1, pixel value 1 → +1 (same reasoning as Lab 1).

**One-hot bipolar target** for class $c$ (5 classes):
$$
t_j = \begin{cases} +1 & j = c \\ -1 & j \neq c \end{cases}
$$

**Net input for output neuron $j$:**
$$
y_{in,j} = \sum_{i=1}^{25} w_{ij}x_i + b_j
$$

**Delta rule update, extended to multiple outputs** (applied for every output neuron $j$ simultaneously):
$$
\Delta w_{ij} = \eta (t_j - y_j)x_i
$$

This is compactly computed as an **outer product** of the input vector and the error vector:
$$
\Delta W = \eta \, (x \otimes e), \quad \text{where } e = t - y
$$

**Classification decision (winner-take-all):**
$$
\hat{c} = \arg\max_j (y_{in,j})
$$

### Why Flattening Loses Information

Flattening destroys the 2D spatial relationships between pixels (e.g., "this pixel is directly above that one" is lost). This is a fundamental limitation that motivates **Convolutional Neural Networks** (Lab 5), which preserve spatial structure.

### Key Viva Points
- One-hot encoding is required whenever there are more than 2 classes and outputs aren't ordinal.
- Noise tolerance: because weights encode a *distributed* pattern across all 25 pixels, a couple of flipped pixels usually doesn't change the argmax decision.

---

## Lab 5: CNN for Face/Fruit/Bird Classification

### Concept

A **Convolutional Neural Network (CNN)** preserves 2D spatial structure by sliding small filters across the image instead of flattening it. This makes CNNs vastly more effective and efficient for image tasks.

### Architecture

```
Input Image (H x W x 3)
      │
   [Conv2D + ReLU]   ← detect low-level features (edges, colors)
      │
   [MaxPooling2D]    ← downsample, keep strongest signals
      │
   [Conv2D + ReLU]   ← detect higher-level combinations of features
      │
   [MaxPooling2D]
      │
   [Flatten]         ← convert final feature maps to 1D vector
      │
   [Dense + ReLU]    ← fully-connected reasoning layer
      │
   [Dropout]         ← regularization (prevent overfitting)
      │
   [Dense + Softmax] ← final class probabilities
```

### Equations

**Convolution operation** (2D, for a filter/kernel $K$ of size $m \times m$ over image $I$):
$$
(I * K)(x,y) = \sum_{i=0}^{m-1}\sum_{j=0}^{m-1} I(x+i, y+j)\cdot K(i,j)
$$

**Output feature map size** (no padding, stride $s$, input size $n$, filter size $f$):
$$
\text{Output size} = \left\lfloor \frac{n - f}{s} \right\rfloor + 1
$$

**ReLU activation** (introduces non-linearity):
$$
f(x) = \max(0, x)
$$

**Max Pooling** (for a 2×2 window):
$$
\text{pool}(x) = \max(x_{1}, x_{2}, x_{3}, x_{4})
$$

**Softmax** (final layer, converts raw scores/logits $z$ into probabilities across $K$ classes):
$$
\text{softmax}(z_i) = \frac{e^{z_i}}{\sum_{k=1}^{K} e^{z_k}}
$$

**Categorical Cross-Entropy Loss** (for training):
$$
L = -\sum_{k=1}^{K} t_k \log(\hat{y}_k)
$$

### Why Convolution Works Better Than Fully-Connected Layers for Images

1. **Weight sharing**: the same filter (small set of weights) is reused across the entire image — a cat's ear is detected the same way whether it's top-left or bottom-right (**translation invariance**).
2. **Local connectivity**: each neuron only looks at a small local region (receptive field), matching the intuition that nearby pixels are more related than distant ones.
3. **Parameter efficiency**: far fewer weights than a fully-connected layer processing the same image, reducing overfitting risk.

### Dropout (Regularization)

During training, randomly "switches off" a fraction $p$ of neurons each forward pass:
$$
\text{Dropout}(x_i) = \begin{cases} 0 & \text{with probability } p \\ x_i / (1-p) & \text{with probability } 1-p \end{cases}
$$
This prevents the network from over-relying on any single neuron, improving generalization.

### Key Viva Points
- Filters/kernels are **learned**, not hand-designed — backpropagation trains them just like any other weight.
- Pooling provides slight translation invariance and reduces computation.
- Overfitting is diagnosed by a growing gap between training accuracy and validation accuracy.

---

## Lab 6: Backpropagation on a 3-Layer ANN

### Concept

**Backpropagation** trains multi-layer networks by computing how much each weight contributed to the final error, using the **chain rule of calculus**, and propagating this error information **backward** from output to input.

### Given Network (from exam diagram)

```
x1=0.05 ──w1=0.15──┐         ┌──w5=0.40──┐
                     ├──►[H1]──┤            ├──►[y1]  target=0.01
x1 ──w3=0.25──┐      │         w6=0.45──┐  │
               ├──►[H2]         w7=0.50─┼──┴──►[y2]  target=0.99
x2=0.10──w2=0.20┘     w8=0.55──┘
        ──w4=0.30─┘

bias b1=0.35 → added to H1, H2
bias b2=0.60 → added to y1, y2
Activation: sigmoid throughout
```

### Step 1 — Forward Pass Equations

**Hidden layer:**
$$
net_{h1} = w_1x_1 + w_2x_2 + b_1, \qquad out_{h1} = \sigma(net_{h1})
$$
$$
net_{h2} = w_3x_1 + w_4x_2 + b_1, \qquad out_{h2} = \sigma(net_{h2})
$$

**Output layer:**
$$
net_{o1} = w_5 \cdot out_{h1} + w_6 \cdot out_{h2} + b_2, \qquad out_{o1} = \sigma(net_{o1})
$$
$$
net_{o2} = w_7 \cdot out_{h1} + w_8 \cdot out_{h2} + b_2, \qquad out_{o2} = \sigma(net_{o2})
$$

**Sigmoid activation function:**
$$
\sigma(x) = \frac{1}{1+e^{-x}}
$$

**Sigmoid derivative (key property — reuses the forward-pass output):**
$$
\sigma'(x) = \sigma(x)\big(1-\sigma(x)\big)
$$

### Step 2 — Total Error

$$
E_{total} = \sum E_k = \frac{1}{2}(T_1 - out_{o1})^2 + \frac{1}{2}(T_2 - out_{o2})^2
$$

### Step 3 — Backward Pass: Output Layer Weights ($w_5, w_6, w_7, w_8$)

Using the chain rule for $w_5$:
$$
\frac{\partial E_{total}}{\partial w_5} = \frac{\partial E_{total}}{\partial out_{o1}} \cdot \frac{\partial out_{o1}}{\partial net_{o1}} \cdot \frac{\partial net_{o1}}{\partial w_5}
$$

Each term:
$$
\frac{\partial E_{total}}{\partial out_{o1}} = -(T_1 - out_{o1})
$$
$$
\frac{\partial out_{o1}}{\partial net_{o1}} = out_{o1}(1-out_{o1})
$$
$$
\frac{\partial net_{o1}}{\partial w_5} = out_{h1}
$$

Define $\delta_{o1}$ (the "delta"/local gradient for output neuron 1):
$$
\delta_{o1} = -(T_1 - out_{o1})\cdot out_{o1}(1-out_{o1})
$$
$$
\frac{\partial E_{total}}{\partial w_5} = \delta_{o1} \cdot out_{h1}
$$

(Same pattern applies to $w_6, w_7, w_8$, using $\delta_{o1}, \delta_{o2}$ appropriately.)

### Step 4 — Backward Pass: Hidden Layer Weights ($w_1, w_2, w_3, w_4$)

**This is the essence of "back"-propagation.** Since $out_{h1}$ feeds into **both** $o1$ and $o2$, its error contribution must sum both paths:
$$
\frac{\partial E_{total}}{\partial out_{h1}} = \delta_{o1}\cdot w_5 + \delta_{o2}\cdot w_7
$$
$$
\delta_{h1} = \left(\delta_{o1}w_5 + \delta_{o2}w_7\right)\cdot out_{h1}(1-out_{h1})
$$
$$
\frac{\partial E_{total}}{\partial w_1} = \delta_{h1}\cdot x_1
$$

### Step 5 — Gradient Descent Weight Update

$$
w(\text{new}) = w(\text{old}) - \eta\cdot\frac{\partial E_{total}}{\partial w}
$$

### Summary Diagram — Direction of Information Flow

```
FORWARD PASS:   x1,x2 ──► hidden (H1,H2) ──► output (y1,y2) ──► Error
                   (compute activations left to right)

BACKWARD PASS:  δ_o1,δ_o2 ──► δ_h1,δ_h2 ──► gradients for w1..w4
                   (compute deltas right to left, using chain rule)
```

### Key Viva Points
- Backprop = repeated, systematic application of the **chain rule**.
- The "delta" ($\delta$) at each neuron is reused — it's computed once and used for every weight feeding into that neuron, which is what makes backprop efficient.
- **Vanishing gradient problem**: sigmoid's derivative is always $\leq 0.25$; in deep networks, multiplying many such small numbers together (chain rule across many layers) makes early-layer gradients shrink toward zero, slowing learning. This motivates ReLU in modern deep networks.

---

## Lab 7: Transfer Learning with ResNet-50

### Concept

**Transfer learning** reuses a network already trained on a huge dataset (ImageNet: 1.4 million images, 1000 classes) and adapts it to a new, smaller task — instead of training a new CNN from scratch.

### Architecture

```
┌─────────────────────────────────────┐
│   ResNet-50 Convolutional Base       │   ← PRETRAINED, FROZEN
│   (already knows edges, textures,    │     (weights unchanged
│    shapes, object parts...)          │      during initial training)
└─────────────────────────────────────┘
              │
      [GlobalAveragePooling2D]          ← condense feature maps
              │
      [Dense(128, relu)]                ← NEW, trainable
              │
      [Dropout]
              │
      [Dense(num_classes, softmax)]     ← NEW, trainable
```

### Two-Phase Training Strategy

**Phase 1 — Feature extraction (base frozen):**
$$
\theta_{base} \text{ frozen (no gradient updates)}, \quad \text{only } \theta_{head} \text{ updated}
$$
Only the small new head (a few hundred thousand parameters) is trained — fast, and safe even with a small dataset.

**Phase 2 — Fine-tuning (unfreeze last few layers):**
$$
\theta_{base}^{(last\ k\ layers)} \text{ unfrozen}, \quad \eta_{fine-tune} \ll \eta_{initial}
$$
A **very small learning rate** is used to gently adapt the highest-level pretrained features without destroying previously learned knowledge (avoiding "catastrophic forgetting").

### Why Freeze Layers?

- Early CNN layers learn **generic** features (edges, colors, simple textures) — useful for almost any visual task.
- Later CNN layers learn **task-specific** features (closer to whole-object recognition) — these benefit most from fine-tuning.
- Freezing prevents a small new dataset from "overwriting" millions of images' worth of learned knowledge.

### Global Average Pooling vs Flatten

$$
\text{GAP}(\text{feature map}) = \frac{1}{H\times W}\sum_{i=1}^{H}\sum_{j=1}^{W} F(i,j)
$$

GAP collapses each entire feature map to a single average number — drastically fewer parameters than `Flatten()`, reducing overfitting risk.

### Key Viva Points
- Transfer learning is most beneficial when your own dataset is small.
- `include_top=False` removes the original 1000-class ImageNet classification head, since we need a different number of output classes.
- Fine-tuning learning rate must be much smaller than normal training LR to avoid destructive updates.

---

## Lab 8: GAN for Generating Handwritten Digits (MNIST)

### Concept

A **Generative Adversarial Network (GAN)** consists of two networks trained in competition:
- **Generator (G)**: creates fake images from random noise, trying to fool the Discriminator.
- **Discriminator (D)**: classifies images as real or fake, trying to catch the Generator's fakes.

### Architecture

```
Random noise z (latent vector, e.g. 100 numbers)
        │
   [Generator G]
        │
   Fake image ──┐
                 ├──► [Discriminator D] ──► P(real) ∈ [0,1]
   Real image ──┘
   (from MNIST)
```

### The Minimax Game (Core Equation)

GANs are formally trained by solving:
$$
\min_G \max_D \; V(D,G) = \mathbb{E}_{x\sim p_{data}}[\log D(x)] + \mathbb{E}_{z\sim p_z}[\log(1-D(G(z)))]
$$

- $D$ tries to **maximize** this — correctly identify real ($D(x)\to1$) and fake ($D(G(z))\to0$).
- $G$ tries to **minimize** this — make $D(G(z))\to1$ (fool the discriminator).

### Practical Loss Functions (Binary Cross-Entropy based)

**Discriminator loss:**
$$
L_D = -\Big[\log D(x_{real}) + \log(1-D(G(z)))\Big]
$$

**Generator loss** (non-saturating version, commonly used in practice):
$$
L_G = -\log D(G(z))
$$
(i.e., Generator is rewarded when the Discriminator mistakenly outputs a high probability of "real" for a fake image.)

### Training Loop Diagram

```
for each training step:
    1. Sample noise z, generate fake images: G(z)
    2. Get D's prediction on real images AND fake images
    3. Compute L_D → update ONLY Discriminator's weights
    4. Compute L_G → update ONLY Generator's weights
    (two SEPARATE gradient computations, two separate optimizers)
```

### Key Architectural Notes

- **Conv2DTranspose** ("deconvolution"): the reverse of Conv2D — it *increases* spatial dimensions (e.g., 7×7 → 14×14 → 28×28), used in the Generator to grow noise into a full image.
- **tanh** output activation in Generator → outputs range [-1, 1]; real images must be scaled to match.
- **LeakyReLU** (instead of plain ReLU) is preferred in GANs to keep gradients flowing even for negative inputs:
$$
\text{LeakyReLU}(x) = \begin{cases} x & x > 0 \\ \alpha x & x \leq 0 \end{cases} \quad (\alpha \approx 0.2)
$$

### Why Loss Oscillates (Not Monotonically Decreasing)

Because it's a two-player adversarial game (not a single optimization), improvement by one network makes the other's task harder, and vice-versa. Oscillation is **expected, normal GAN behavior** — success is judged by the visual quality/diversity of generated samples, not by loss reaching zero.

### Key Viva Points
- **Mode collapse**: Generator finds one output that reliably fools D and stops producing diverse outputs — a known failure mode.
- GANs are evaluated via visual inspection or metrics like FID (Fréchet Inception Distance), not simple accuracy.

---

## Lab 9: Speech Recognition (Numbers 1-4) using ANN

### Concept

Raw audio (thousands of samples/second) is too high-dimensional and position-sensitive to feed directly into an ANN. **Feature extraction** compresses audio into a compact, meaningful representation first.

### Pipeline Diagram

```
Raw audio waveform (1D signal, e.g. 16000 samples/sec)
        │
  [MFCC Feature Extraction]
        │
  MFCC matrix (13 coefficients x T time-frames)
        │
  [Average across time frames]
        │
  Fixed-length feature vector (13 numbers)
        │
  [ANN: Dense → Dense → Softmax]
        │
  Predicted class (one, two, three, four)
```

### MFCC (Mel-Frequency Cepstral Coefficients) — Conceptual Steps

1. **Framing**: split audio into short overlapping time windows (e.g., 25ms).
2. **FFT (Fast Fourier Transform)**: convert each frame from time domain to frequency domain.
$$
X(k) = \sum_{n=0}^{N-1} x(n) e^{-i2\pi kn/N}
$$
3. **Mel filterbank**: warp the frequency axis onto the **Mel scale**, which mimics human pitch perception (more sensitive at low frequencies, less at high):
$$
m = 2595 \log_{10}\left(1 + \frac{f}{700}\right)
$$
4. **Log**: take the log of the filterbank energies (mimics human perception of loudness).
5. **DCT (Discrete Cosine Transform)**: decorrelates the log energies, compressing them into a small number of coefficients (typically 13).

### Why Average Across Time?

Different audio clips have different durations → different numbers of time frames. Averaging collapses this variable-length matrix into one **fixed-size vector**, which a standard ANN requires (no variable input size).
$$
\bar{x}_i = \frac{1}{T}\sum_{t=1}^{T} MFCC_{i,t}, \quad i = 1, ..., 13
$$

### ANN Classifier

Standard feedforward network (same math as earlier labs):
$$
h = \text{ReLU}(W_1 x + b_1), \qquad \hat{y} = \text{softmax}(W_2 h + b_2)
$$

### Key Viva Points
- MFCC is the industry-standard feature for classical speech recognition (pre-deep-learning-era and still widely used).
- Averaging discards temporal ordering information — for tasks needing that (continuous speech, longer sentences), RNNs/LSTMs/Transformers that process sequences natively are preferred over simple averaging + ANN.

---

## Lab 10: SVM for Purchase Classification Prediction

### Concept

A **Support Vector Machine (SVM)** finds the decision boundary (hyperplane) that **maximizes the margin** — the distance between the boundary and the nearest points of each class — rather than just any separating line.

### Architecture / Geometric Diagram

```
        margin        margin
          │              │
   o   o  │      ○       │   x   x
      o   │   ○     ○    │  x   x
    o     │  ○   ○       │    x
──────────┼──────────────┼────────── decision boundary
          │  (support     │
          │   vectors      │
          │   circled)     │
```
Points closest to the boundary (circled) are the **support vectors** — only these determine the boundary; all other points could be removed without changing it.

### Equations

**Decision hyperplane:**
$$
w \cdot x + b = 0
$$

**Classification rule:**
$$
\hat{y} = \text{sign}(w\cdot x + b)
$$

**Margin width** (distance between the two parallel margin boundaries):
$$
\text{margin} = \frac{2}{\|w\|}
$$

**SVM Optimization Objective** (hard margin, linearly separable case):
$$
\min_{w,b} \frac{1}{2}\|w\|^2 \quad \text{subject to} \quad t_i(w\cdot x_i + b) \geq 1 \; \forall i
$$

**Soft margin** (allowing some misclassification, via slack variables $\xi_i$):
$$
\min_{w,b,\xi} \frac{1}{2}\|w\|^2 + C\sum_{i=1}^{n}\xi_i \quad \text{subject to} \quad t_i(w\cdot x_i+b)\geq 1-\xi_i,\; \xi_i\geq 0
$$

- **C parameter**: controls the trade-off. Small C → prioritize wide margin, tolerate more misclassification (softer boundary). Large C → penalize misclassification heavily, narrower/more complex boundary (risk of overfitting).

### The Kernel Trick

When data isn't linearly separable, SVM implicitly maps inputs into a higher-dimensional space via a **kernel function** $K(x_i, x_j)$, without ever explicitly computing the transformation:

**Linear kernel:**
$$
K(x_i, x_j) = x_i \cdot x_j
$$

**RBF (Gaussian) kernel** (most common for non-linear boundaries):
$$
K(x_i, x_j) = \exp\left(-\gamma \|x_i - x_j\|^2\right)
$$
- **gamma (γ)**: controls how far a single training point's influence reaches. Small γ → smooth/simple boundary. Large γ → tight, wiggly boundary (risk of overfitting).

**Polynomial kernel:**
$$
K(x_i, x_j) = (x_i \cdot x_j + c)^d
$$

### Why Feature Scaling Matters

SVM's decision boundary is based on **geometric distance**. A feature with a much larger numeric range (e.g., Salary: 15,000–150,000) would dominate the distance calculation over a smaller-range feature (e.g., Age: 18–60) unless both are standardized:
$$
x_{scaled} = \frac{x - \mu}{\sigma}
$$

### Key Viva Points
- Support vectors are the *only* points that matter for the final boundary.
- Confusion Matrix terms: True Positive, True Negative, False Positive, False Negative — used to compute precision, recall, F1-score, accuracy.
$$
\text{Accuracy} = \frac{TP+TN}{TP+TN+FP+FN}, \quad \text{Precision} = \frac{TP}{TP+FP}, \quad \text{Recall} = \frac{TP}{TP+FN}
$$

---

## Lab 11: PCA for Dimensionality Reduction

### Concept

**Principal Component Analysis (PCA)** is an **unsupervised** technique that finds new axes (principal components) capturing the maximum variance in the data, allowing high-dimensional data to be represented with fewer dimensions while retaining most of its information.

### Conceptual Diagram

```
Original 2D data (correlated features):        After PCA rotation:

    x2                                            PC2
    │      ●                                        │
    │    ●   ●                                       │  ●
    │  ●   ●   ●        ──────►                      │●   ●
    │    ●   ●                                  ─────┼──────● PC1
    │  ●   ●                                         │  ●
    └──────────── x1                                 │
                                            (PC1 = direction of max variance,
                                             PC2 = perpendicular, 2nd-most variance)
```

### Steps and Equations

**Step 1 — Standardize the data:**
$$
x_{scaled} = \frac{x-\mu}{\sigma}
$$

**Step 2 — Compute the covariance matrix** (for $d$ features):
$$
\Sigma = \frac{1}{n-1}X^TX \quad (\text{after centering } X)
$$

**Step 3 — Eigen-decomposition:**
$$
\Sigma v_i = \lambda_i v_i
$$
- $v_i$ = eigenvector = direction of the $i$-th principal component.
- $\lambda_i$ = eigenvalue = amount of variance captured along that direction.

**Step 4 — Sort eigenvectors by eigenvalue (descending)** and select the top $k$ to form the projection matrix $W = [v_1, v_2, ..., v_k]$.

**Step 5 — Project data onto the new, lower-dimensional space:**
$$
X_{reduced} = X_{scaled} \cdot W
$$

### Explained Variance Ratio

$$
\text{explained variance ratio of PC}_i = \frac{\lambda_i}{\sum_{j=1}^{d}\lambda_j}
$$

**Cumulative explained variance** (used to decide how many components to keep — e.g., keep enough to cross 95%):
$$
\text{cumulative}(k) = \sum_{i=1}^{k}\frac{\lambda_i}{\sum_{j=1}^{d}\lambda_j}
$$

### Scree Plot (conceptual)

```
Variance
Explained │██
    100%  │██
          │██  ██
          │██  ██
          │██  ██  ▓▓
          │██  ██  ▓▓  ░░
          └────────────────
            PC1 PC2 PC3 PC4
   (keep components up to where the curve "elbows"/flattens)
```

### Why Standardize Before PCA?

PCA maximizes **variance**, and variance is scale-dependent. A feature measured in larger raw numbers (e.g., salary) would appear artificially "more important"/high-variance than one measured in small numbers (e.g., age) unless both are standardized first — same underlying reasoning as SVM.

### Key Viva Points
- PCA is **unsupervised** — it never uses class labels, only feature values.
- Principal components are always **orthogonal** (uncorrelated) to each other by construction — this ensures no redundant information between components.
- **Loadings** (the eigenvector values) tell you how much each *original* feature contributes to a given principal component — useful for interpreting what a component "means."
- PCA is commonly used as a **preprocessing step** before other ML algorithms to reduce noise, computation time, and overfitting risk (the "curse of dimensionality").

---

## Quick Cross-Lab Comparison Table

| Lab | Algorithm Type | Learning Paradigm | Key Equation |
|---|---|---|---|
| 1 | Perceptron | Supervised, binary classification | $w=w+\eta t x$ |
| 2 | Delta Rule (SGD) | Supervised, regression-style | $w=w+\eta(t-y)x$ |
| 3 | Delta Rule (Batch) | Supervised | Averaged gradient update |
| 4 | Multi-output Delta Rule | Supervised, multi-class | Outer product update |
| 5 | CNN | Supervised, deep learning | Convolution + Cross-entropy |
| 6 | Backpropagation | Supervised, deep learning | Chain rule gradients |
| 7 | Transfer Learning | Supervised, deep learning | Frozen weights + fine-tuning |
| 8 | GAN | Unsupervised/self-supervised, adversarial | Minimax game |
| 9 | ANN + MFCC | Supervised, deep learning | Feature extraction + Dense layers |
| 10 | SVM | Supervised, classical ML | Margin maximization |
| 11 | PCA | Unsupervised, classical ML | Eigen-decomposition |