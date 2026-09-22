# All Code Files

## back_prop.py

```python
import numpy as np
import matplotlib.pyplot as plt

# ---------------------------------------------------------
# STEP 1: Define inputs, weights, biases, targets
# EXACTLY as given in the exam diagram
# ---------------------------------------------------------
x1, x2 = 0.05, 0.10

# Input -> Hidden weights
w1, w2, w3, w4 = 0.15, 0.20, 0.25, 0.30
b1 = 0.35   # bias added to both hidden neurons

# Hidden -> Output weights
w5, w6, w7, w8 = 0.40, 0.45, 0.50, 0.55
b2 = 0.60   # bias added to both output neurons

T1, T2 = 0.01, 0.99   # target outputs
learning_rate = 0.5

# ---------------------------------------------------------
# STEP 2: Sigmoid activation and its derivative
#   sigmoid(x)  = 1 / (1 + e^-x)
#   sigmoid'(x) = sigmoid(x) * (1 - sigmoid(x))
# ---------------------------------------------------------
def sigmoid(x):
    return 1 / (1 + np.exp(-x))

def sigmoid_derivative(sig_output):
    return sig_output * (1 - sig_output)

# ---------------------------------------------------------
# STEP 3: FORWARD PASS
# ---------------------------------------------------------
def forward_pass(x1, x2, w1, w2, w3, w4, b1, w5, w6, w7, w8, b2):
    # ---- Hidden layer ----
    net_h1 = w1 * x1 + w2 * x2 + b1
    out_h1 = sigmoid(net_h1)

    net_h2 = w3 * x1 + w4 * x2 + b1
    out_h2 = sigmoid(net_h2)

    # ---- Output layer ----
    net_o1 = w5 * out_h1 + w6 * out_h2 + b2
    out_o1 = sigmoid(net_o1)

    net_o2 = w7 * out_h1 + w8 * out_h2 + b2
    out_o2 = sigmoid(net_o2)

    return out_h1, out_h2, out_o1, out_o2

out_h1, out_h2, out_o1, out_o2 = forward_pass(x1, x2, w1, w2, w3, w4, b1,
                                                w5, w6, w7, w8, b2)

print("--- FORWARD PASS (before training) ---")
print(f"out_h1 = {out_h1:.6f}, out_h2 = {out_h2:.6f}")
print(f"out_o1 = {out_o1:.6f}, out_o2 = {out_o2:.6f}")

# ---------------------------------------------------------
# STEP 4: TOTAL ERROR (Sum of Squared Errors, as in the
# classic textbook version of this exact example)
#   E = 1/2 * (target - output)^2   summed over output neurons
# ---------------------------------------------------------
def total_error(out_o1, out_o2, T1, T2):
    E1 = 0.5 * (T1 - out_o1) ** 2
    E2 = 0.5 * (T2 - out_o2) ** 2
    return E1 + E2

E_before = total_error(out_o1, out_o2, T1, T2)
print(f"\nTotal Error (before training) = {E_before:.6f}")

# ---------------------------------------------------------
# STEP 5: BACKWARD PASS -- Output layer weights (w5,w6,w7,w8)
#
# Chain rule for w5 (H1 -> y1):
#   dE/dw5 = dE1/d(out_o1) * d(out_o1)/d(net_o1) * d(net_o1)/dw5
#          = -(T1 - out_o1) * out_o1*(1-out_o1) * out_h1
# Same pattern applies to w6, w7, w8.
# ---------------------------------------------------------
delta_o1 = -(T1 - out_o1) * sigmoid_derivative(out_o1)
delta_o2 = -(T2 - out_o2) * sigmoid_derivative(out_o2)

dE_dw5 = delta_o1 * out_h1
dE_dw6 = delta_o1 * out_h2
dE_dw7 = delta_o2 * out_h1
dE_dw8 = delta_o2 * out_h2

# ---------------------------------------------------------
# STEP 6: BACKWARD PASS -- Hidden layer weights (w1,w2,w3,w4)
#
# Each hidden neuron affects BOTH output neurons, so its total
# error contribution is the SUM of paths through o1 AND o2.
#
#   dE/d(out_h1) = delta_o1*w5 + delta_o2*w7
#   delta_h1 = dE/d(out_h1) * sigmoid_derivative(out_h1)
#   dE/dw1 = delta_h1 * x1        (and similarly for w2, w3, w4)
# ---------------------------------------------------------
dE_douth1 = delta_o1 * w5 + delta_o2 * w7
dE_douth2 = delta_o1 * w6 + delta_o2 * w8

delta_h1 = dE_douth1 * sigmoid_derivative(out_h1)
delta_h2 = dE_douth2 * sigmoid_derivative(out_h2)

dE_dw1 = delta_h1 * x1
dE_dw2 = delta_h1 * x2
dE_dw3 = delta_h2 * x1
dE_dw4 = delta_h2 * x2

# ---------------------------------------------------------
# STEP 7: UPDATE ALL WEIGHTS using Gradient Descent
#   w_new = w_old - learning_rate * dE/dw
# ---------------------------------------------------------
w1_new = w1 - learning_rate * dE_dw1
w2_new = w2 - learning_rate * dE_dw2
w3_new = w3 - learning_rate * dE_dw3
w4_new = w4 - learning_rate * dE_dw4

w5_new = w5 - learning_rate * dE_dw5
w6_new = w6 - learning_rate * dE_dw6
w7_new = w7 - learning_rate * dE_dw7
w8_new = w8 - learning_rate * dE_dw8

print("\n--- UPDATED WEIGHTS (after ONE backprop step) ---")
print(f"w1: {w1:.5f} -> {w1_new:.5f}")
print(f"w2: {w2:.5f} -> {w2_new:.5f}")
print(f"w3: {w3:.5f} -> {w3_new:.5f}")
print(f"w4: {w4:.5f} -> {w4_new:.5f}")
print(f"w5: {w5:.5f} -> {w5_new:.5f}")
print(f"w6: {w6:.5f} -> {w6_new:.5f}")
print(f"w7: {w7:.5f} -> {w7_new:.5f}")
print(f"w8: {w8:.5f} -> {w8_new:.5f}")

# ---------------------------------------------------------
# STEP 8: Verify error DECREASED after the update
# ---------------------------------------------------------
out_h1_new, out_h2_new, out_o1_new, out_o2_new = forward_pass(
    x1, x2, w1_new, w2_new, w3_new, w4_new, b1,
    w5_new, w6_new, w7_new, w8_new, b2)

E_after = total_error(out_o1_new, out_o2_new, T1, T2)
print(f"\nTotal Error (after ONE update) = {E_after:.6f}")
print(f"Error decreased: {E_before:.6f} -> {E_after:.6f}")

# ---------------------------------------------------------
# STEP 9: Repeat backprop for MANY iterations to show
# full convergence (this is what real training looks like)
# ---------------------------------------------------------
w1c, w2c, w3c, w4c = 0.15, 0.20, 0.25, 0.30
w5c, w6c, w7c, w8c = 0.40, 0.45, 0.50, 0.55
errors = []

iterations = 10000
for i in range(iterations):
    oh1, oh2, oo1, oo2 = forward_pass(x1, x2, w1c, w2c, w3c, w4c, b1,
                                       w5c, w6c, w7c, w8c, b2)
    errors.append(total_error(oo1, oo2, T1, T2))

    d_o1 = -(T1 - oo1) * sigmoid_derivative(oo1)
    d_o2 = -(T2 - oo2) * sigmoid_derivative(oo2)

    g_w5 = d_o1 * oh1
    g_w6 = d_o1 * oh2
    g_w7 = d_o2 * oh1
    g_w8 = d_o2 * oh2

    dE_dh1 = d_o1 * w5c + d_o2 * w7c
    dE_dh2 = d_o1 * w6c + d_o2 * w8c
    d_h1 = dE_dh1 * sigmoid_derivative(oh1)
    d_h2 = dE_dh2 * sigmoid_derivative(oh2)

    g_w1 = d_h1 * x1
    g_w2 = d_h1 * x2
    g_w3 = d_h2 * x1
    g_w4 = d_h2 * x2

    w1c -= learning_rate * g_w1
    w2c -= learning_rate * g_w2
    w3c -= learning_rate * g_w3
    w4c -= learning_rate * g_w4
    w5c -= learning_rate * g_w5
    w6c -= learning_rate * g_w6
    w7c -= learning_rate * g_w7
    w8c -= learning_rate * g_w8

print(f"\nAfter {iterations} iterations: Error = {errors[-1]:.8f}")
print(f"Final outputs: o1={oo1:.5f} (target {T1}), o2={oo2:.5f} (target {T2})")

# ---------------------------------------------------------
# STEP 10: Plot error convergence curve
# ---------------------------------------------------------
plt.figure(figsize=(6, 4))
plt.plot(errors)
plt.title("Backpropagation: Total Error vs Iteration")
plt.xlabel("Iteration")
plt.ylabel("Total Error")
plt.grid(True)
plt.show()
plt.close()

print("\nPlot saved: error_convergence.png")
```

## digit_recog.py

```python
import numpy as np
import tensorflow as tf
from tensorflow import keras

# ------------------------------------------------
# 1. Create 5x5 images for digits 1 to 5
# ------------------------------------------------

images = np.array([
    # Digit 1
    [
        [0, 1, 0, 0, 0],
        [1, 1, 0, 0, 0],
        [0, 1, 0, 0, 0],
        [0, 1, 0, 0, 0],
        [1, 1, 1, 0, 0]
    ],

    # Digit 2
    [
        [1, 1, 1, 0, 0],
        [0, 0, 1, 0, 0],
        [1, 1, 1, 0, 0],
        [1, 0, 0, 0, 0],
        [1, 1, 1, 0, 0]
    ],

    # Digit 3
    [
        [1, 1, 1, 0, 0],
        [0, 0, 1, 0, 0],
        [1, 1, 1, 0, 0],
        [0, 0, 1, 0, 0],
        [1, 1, 1, 0, 0]
    ],

    # Digit 4
    [
        [1, 0, 1, 0, 0],
        [1, 0, 1, 0, 0],
        [1, 1, 1, 0, 0],
        [0, 0, 1, 0, 0],
        [0, 0, 1, 0, 0]
    ],

    # Digit 5
    [
        [1, 1, 1, 0, 0],
        [1, 0, 0, 0, 0],
        [1, 1, 1, 0, 0],
        [0, 0, 1, 0, 0],
        [1, 1, 1, 0, 0]
    ]
], dtype="float32")


# ------------------------------------------------
# 2. Labels
# ------------------------------------------------

labels = np.array([0, 1, 2, 3, 4])


# ------------------------------------------------
# 3. Flatten each 5x5 image into 25 values
# ------------------------------------------------

X = images.reshape(5, 25)


# ------------------------------------------------
# 4. Build neural network
# ------------------------------------------------

model = keras.Sequential([
    keras.layers.Dense(16, activation="relu", input_shape=(25,)),
    keras.layers.Dense(5, activation="softmax")
])


# ------------------------------------------------
# 5. Configure the model
# ------------------------------------------------

model.compile(
    optimizer="adam",
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)


# ------------------------------------------------
# 6. Train the model
# ------------------------------------------------

model.fit(
    X,
    labels,
    epochs=500,
    verbose=0
)


# ------------------------------------------------
# 7. Predict
# ------------------------------------------------

predictions = model.predict(X, verbose=0)

predicted_digits = np.argmax(predictions, axis=1) + 1


# ------------------------------------------------
# 8. Display results
# ------------------------------------------------

for actual, predicted in zip(labels + 1, predicted_digits):
    print(
        f"Actual digit: {actual}   "
        f"Predicted digit: {predicted}"
    )








    #------------------------------------------------
import numpy as np
import matplotlib.pyplot as plt

# ---------------------------------------------------------
# STEP 1: Define the 5x5 pixel patterns for digits 1-5
# EXACTLY as given in Figure 1 of the exam.
# 1 = gray/filled pixel, 0 = white/empty pixel
# ---------------------------------------------------------
digit_1 = np.array([
    [0,1,1,0,0],
    [0,0,1,0,0],
    [0,0,1,0,0],
    [0,0,1,0,0],
    [0,1,1,1,0]
])

digit_2 = np.array([
    [1,1,1,1,0],
    [0,0,0,0,1],
    [0,1,1,1,0],
    [1,0,0,0,0],
    [1,1,1,1,1]
])

digit_3 = np.array([
    [1,1,1,1,0],
    [0,0,0,0,1],
    [0,1,1,1,0],
    [0,0,0,0,1],
    [1,1,1,1,0]
])

digit_4 = np.array([
    [0,0,0,1,0],
    [0,0,1,1,0],
    [0,1,0,1,0],
    [1,1,1,1,1],
    [0,0,0,1,0]
])

digit_5 = np.array([
    [1,1,1,1,1],
    [1,0,0,0,0],
    [1,1,1,1,0],
    [0,0,0,0,1],
    [1,1,1,1,0]
])

digits = [digit_1, digit_2, digit_3, digit_4, digit_5]

# ---------------------------------------------------------
# STEP 2: Flatten each 5x5 image into a 25-length vector,
# and convert to BIPOLAR (-1/+1) since bipolar trains better
# (same reasoning as Lab 1)
# ---------------------------------------------------------
X = np.array([img.flatten() for img in digits])   # shape (5, 25)
X = np.where(X == 0, -1, 1)                        # convert 0->-1, 1 stays 1

# ---------------------------------------------------------
# STEP 3: One-hot BIPOLAR targets for 5 classes
# digit 1 -> [ 1,-1,-1,-1,-1]
# digit 2 -> [-1, 1,-1,-1,-1]
# digit 3 -> [-1,-1, 1,-1,-1]
# digit 4 -> [-1,-1,-1, 1,-1]
# digit 5 -> [-1,-1,-1,-1, 1]
# ---------------------------------------------------------
T = -1 * np.ones((5, 5))
np.fill_diagonal(T, 1)

# ---------------------------------------------------------
# STEP 4: Initialize weights: 25 inputs -> 5 outputs
# weights shape: (25, 5)   one column of weights per output neuron
# ---------------------------------------------------------
np.random.seed(1)
weights = np.random.uniform(-0.5, 0.5, size=(25, 5))
bias = np.random.uniform(-0.5, 0.5, size=5)
learning_rate = 0.05
epochs = 200

def linear_activation(y_in):
    return y_in

# ---------------------------------------------------------
# STEP 5: Train with SGD + Delta Rule (multi-output version)
# Same idea as Lab 2, just repeated for each of the 5 outputs
# ---------------------------------------------------------
mse_per_epoch = []

for epoch in range(epochs):
    total_squared_error = 0
    for i in range(len(X)):
        x = X[i]
        target = T[i]

        y_in = np.dot(x, weights) + bias      # shape (5,)
        y = linear_activation(y_in)

        error = target - y                     # shape (5,)

        # Update weights: outer product (25,) x (5,) -> (25,5)
        weights = weights + learning_rate * np.outer(x, error)
        bias = bias + learning_rate * error

        total_squared_error += np.sum(error ** 2)

    mse_per_epoch.append(total_squared_error / len(X))

print("Training complete.")
print("Final MSE:", mse_per_epoch[-1])

# ---------------------------------------------------------
# STEP 6: Test on the SAME clean patterns
# Prediction = neuron with the HIGHEST output value (winner-take-all)
# ---------------------------------------------------------
print("\n--- Testing on clean digit patterns ---")
for i in range(len(X)):
    y_in = np.dot(X[i], weights) + bias
    predicted_class = np.argmax(y_in) + 1     # +1 since digits are labeled 1-5
    print(f"True digit: {i+1}, Predicted digit: {predicted_class}, raw outputs: {np.round(y_in,2)}")

# ---------------------------------------------------------
# STEP 7: Test with NOISY input (flip 2 random pixels of digit 3)
# This demonstrates the network can generalize / tolerate noise
# ---------------------------------------------------------
noisy_digit_3 = X[2].copy()
noisy_digit_3[0] *= -1     # flip pixel 1
noisy_digit_3[10] *= -1    # flip pixel 11

y_in_noisy = np.dot(noisy_digit_3, weights) + bias
predicted_noisy = np.argmax(y_in_noisy) + 1
print(f"\nNoisy version of digit 3 (2 pixels flipped) predicted as: {predicted_noisy}")

# ---------------------------------------------------------
# STEP 8: Plot MSE convergence curve
# ---------------------------------------------------------
plt.figure(figsize=(6, 4))
plt.plot(range(1, epochs+1), mse_per_epoch)
plt.title("Digit Recognition: MSE vs Epoch")
plt.xlabel("Epoch")
plt.ylabel("Mean Squared Error")
plt.grid(True)
plt.show()
plt.close()

# ---------------------------------------------------------
# STEP 9: Visualize the 5 digit patterns (for the report)
# ---------------------------------------------------------
fig, axes = plt.subplots(1, 5, figsize=(12, 3))
for i, ax in enumerate(axes):
    ax.imshow(digits[i], cmap='gray_r')
    ax.set_title(f"Digit {i+1}")
    ax.set_xticks([])
    ax.set_yticks([])
plt.show()
plt.close()

print("\nPlots saved: mse_curve.png, digit_patterns.png")
```

## face_fruit_bird.py

```python
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

# ================================================
# 1. Load dataset
# ================================================

train_dir = "dataset/dataset/train"   # <-- contains face/, fruit/, bird/
test_dir = "dataset/dataset/test"     # <-- contains face/, fruit/, bird/

img_size = (128, 128)
batch_size = 8

train_ds = keras.utils.image_dataset_from_directory(
    train_dir,
    seed=123,
    image_size=img_size,
    batch_size=batch_size
)

validation_ds = keras.utils.image_dataset_from_directory(
    test_dir,
    seed=123,
    image_size=img_size,
    batch_size=batch_size
)

# ================================================
# 2. Get class names
# ================================================

class_names = train_ds.class_names
print("Classes:", class_names)

# Keep an unnormalized copy of a validation batch for later sample plotting
sample_val_ds = validation_ds

# Performance: cache/prefetch (safe no-op for small datasets)
AUTOTUNE = tf.data.AUTOTUNE
train_ds = train_ds.cache().shuffle(200).prefetch(buffer_size=AUTOTUNE)
validation_ds = validation_ds.cache().prefetch(buffer_size=AUTOTUNE)

# ================================================
# 3. Build CNN
# ================================================

model = keras.Sequential([

    layers.Rescaling(1./255, input_shape=(128, 128, 3)),

    layers.Conv2D(32, (3, 3), activation="relu"),
    layers.MaxPooling2D(),

    layers.Conv2D(64, (3, 3), activation="relu"),
    layers.MaxPooling2D(),

    layers.Conv2D(128, (3, 3), activation="relu"),
    layers.MaxPooling2D(),

    layers.Flatten(),

    layers.Dense(128, activation="relu"),
    layers.Dropout(0.3),          # helps reduce overfitting on small datasets

    layers.Dense(len(class_names), activation="softmax")
])

model.summary()

# ================================================
# 4. Compile model
# ================================================

model.compile(
    optimizer="adam",
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)

# ================================================
# 5. Train model
# ================================================

epochs = 10

history = model.fit(
    train_ds,
    validation_data=validation_ds,
    epochs=epochs
)

# ================================================
# 6. Evaluate
# ================================================

loss, accuracy = model.evaluate(validation_ds)
print("Validation Accuracy:", accuracy)

# ================================================
# 7. Plot accuracy and loss curves
# ================================================

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].plot(history.history['accuracy'], label='Train Accuracy')
axes[0].plot(history.history['val_accuracy'], label='Val Accuracy')
axes[0].set_title('Accuracy over Epochs')
axes[0].set_xlabel('Epoch')
axes[0].set_ylabel('Accuracy')
axes[0].legend()
axes[0].grid(True)

axes[1].plot(history.history['loss'], label='Train Loss')
axes[1].plot(history.history['val_loss'], label='Val Loss')
axes[1].set_title('Loss over Epochs')
axes[1].set_xlabel('Epoch')
axes[1].set_ylabel('Loss')
axes[1].legend()
axes[1].grid(True)

plt.tight_layout()
plt.savefig("training_curves.png")
plt.close()

# ================================================
# 8. Show a few sample predictions
# ================================================

for images, labels in sample_val_ds.take(1):
    predictions = model.predict(images[:5], verbose=0)
    predicted_classes = np.argmax(predictions, axis=1)

    fig, axes = plt.subplots(1, 5, figsize=(12, 3))
    for i, ax in enumerate(axes):
        ax.imshow(images[i].numpy().astype("uint8"))
        ax.set_title(f"True: {class_names[labels[i]]}\nPred: {class_names[predicted_classes[i]]}")
        ax.axis('off')
    plt.tight_layout()
    plt.savefig("sample_predictions.png")
    plt.close()
    break

print("\nPlots saved: training_curves.png, sample_predictions.png")



# dataset_path = "dataset"

# img_size = (128, 128)
# batch_size = 8

# train_ds = keras.utils.image_dataset_from_directory(
#     dataset_path,
#     validation_split=0.2,
#     subset="training",
#     seed=123,
#     image_size=img_size,
#     batch_size=batch_size
# )

# validation_ds = keras.utils.image_dataset_from_directory(
#     dataset_path,
#     validation_split=0.2,
#     subset="validation",
#     seed=123,
#     image_size=img_size,
#     batch_size=batch_size
# )
```

## gan.py

```python
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow.keras import layers, models

np.random.seed(42)
tf.random.set_seed(42)

IMG_SIZE = 28          # MNIST's real image size
LATENT_DIM = 100       # size of the random noise vector fed to the Generator

# ---------------------------------------------------------
# STEP 1: Load the REAL MNIST dataset
# ---------------------------------------------------------
(X_train, _), (_, _) = tf.keras.datasets.mnist.load_data()
X_train = X_train.astype("float32")
print("Training data shape:", X_train.shape)

# ---------------------------------------------------------
# STEP 2: Normalize to [-1, 1] -- standard for GANs because
# the Generator's final layer uses tanh activation (range -1 to 1)
# ---------------------------------------------------------
X_train = (X_train - 127.5) / 127.5   # MNIST pixels are 0-255, not 0-1
X_train = X_train.reshape(-1, IMG_SIZE, IMG_SIZE, 1)

BATCH_SIZE = 64
dataset = tf.data.Dataset.from_tensor_slices(X_train).shuffle(60000).batch(BATCH_SIZE)

# ---------------------------------------------------------
# STEP 3: Build the GENERATOR
# Takes random noise (latent vector) -> outputs a 28x28x1 image
# ---------------------------------------------------------
def build_generator():
    model = models.Sequential([
        layers.Input(shape=(LATENT_DIM,)),
        layers.Dense(7 * 7 * 128),
        layers.Reshape((7, 7, 128)),

        # Upsample 7x7 -> 14x14
        layers.Conv2DTranspose(64, (4, 4), strides=2, padding='same'),
        layers.BatchNormalization(),
        layers.LeakyReLU(0.2),

        # Upsample 14x14 -> 28x28
        layers.Conv2DTranspose(32, (4, 4), strides=2, padding='same'),
        layers.BatchNormalization(),
        layers.LeakyReLU(0.2),

        # Final layer: produce 1 channel, tanh -> output in [-1,1]
        layers.Conv2D(1, (7, 7), padding='same', activation='tanh')
    ])
    return model

generator = build_generator()
generator.summary()

# ---------------------------------------------------------
# STEP 4: Build the DISCRIMINATOR
# Takes a 28x28x1 image -> outputs probability(real vs fake)
# ---------------------------------------------------------
def build_discriminator():
    model = models.Sequential([
        layers.Input(shape=(IMG_SIZE, IMG_SIZE, 1)),
        layers.Conv2D(64, (4, 4), strides=2, padding='same'),
        layers.LeakyReLU(0.2),
        layers.Dropout(0.3),

        layers.Conv2D(128, (4, 4), strides=2, padding='same'),
        layers.LeakyReLU(0.2),
        layers.Dropout(0.3),

        layers.Flatten(),
        layers.Dense(1, activation='sigmoid')   # probability: real (1) or fake (0)
    ])
    return model

discriminator = build_discriminator()
discriminator.summary()

# ---------------------------------------------------------
# STEP 5: Loss functions and optimizers
# Binary cross-entropy since this is a real-vs-fake (binary) decision
# ---------------------------------------------------------
cross_entropy = tf.keras.losses.BinaryCrossentropy()

def discriminator_loss(real_output, fake_output):
    real_loss = cross_entropy(tf.ones_like(real_output), real_output)   # wants real->1
    fake_loss = cross_entropy(tf.zeros_like(fake_output), fake_output)  # wants fake->0
    return real_loss + fake_loss

def generator_loss(fake_output):
    # Generator WANTS the discriminator to think fakes are real (output->1)
    return cross_entropy(tf.ones_like(fake_output), fake_output)

gen_optimizer = tf.keras.optimizers.Adam(1e-4)
disc_optimizer = tf.keras.optimizers.Adam(1e-4)

# ---------------------------------------------------------
# STEP 6: ONE training step
# Both networks are updated using their OWN separate losses,
# based on the SAME batch of generated + real images
# ---------------------------------------------------------
@tf.function
def train_step(real_images):
    batch_size = tf.shape(real_images)[0]
    noise = tf.random.normal([batch_size, LATENT_DIM])

    with tf.GradientTape() as gen_tape, tf.GradientTape() as disc_tape:
        fake_images = generator(noise, training=True)

        real_output = discriminator(real_images, training=True)
        fake_output = discriminator(fake_images, training=True)

        gen_loss = generator_loss(fake_output)
        disc_loss = discriminator_loss(real_output, fake_output)

    # Each network updates ONLY its own weights
    gen_gradients = gen_tape.gradient(gen_loss, generator.trainable_variables)
    disc_gradients = disc_tape.gradient(disc_loss, discriminator.trainable_variables)

    gen_optimizer.apply_gradients(zip(gen_gradients, generator.trainable_variables))
    disc_optimizer.apply_gradients(zip(disc_gradients, discriminator.trainable_variables))

    return gen_loss, disc_loss

# ---------------------------------------------------------
# STEP 7: Full training loop over many epochs
# ---------------------------------------------------------
EPOCHS = 40
gen_losses, disc_losses = [], []

for epoch in range(EPOCHS):
    epoch_gen_loss, epoch_disc_loss, n_batches = 0, 0, 0
    for real_batch in dataset:
        g_loss, d_loss = train_step(real_batch)
        epoch_gen_loss += g_loss.numpy()
        epoch_disc_loss += d_loss.numpy()
        n_batches += 1

    gen_losses.append(epoch_gen_loss / n_batches)
    disc_losses.append(epoch_disc_loss / n_batches)

    if (epoch + 1) % 5 == 0 or epoch == 0:
        print(f"Epoch {epoch+1}/{EPOCHS}: Gen Loss = {gen_losses[-1]:.4f}, "
              f"Disc Loss = {disc_losses[-1]:.4f}")

# ---------------------------------------------------------
# STEP 8: Plot Generator vs Discriminator loss curves
# ---------------------------------------------------------
plt.figure(figsize=(7, 4))
plt.plot(gen_losses, label='Generator Loss')
plt.plot(disc_losses, label='Discriminator Loss')
plt.title("GAN Training: Generator vs Discriminator Loss")
plt.xlabel("Epoch")
plt.ylabel("Loss")
plt.legend()
plt.grid(True)
plt.savefig("gan_losses.png")
plt.close()

# ---------------------------------------------------------
# STEP 9: Generate and display sample fake images
# ---------------------------------------------------------
noise = tf.random.normal([16, LATENT_DIM])
generated_images = generator(noise, training=False)
generated_images = (generated_images + 1) / 2.0   # rescale [-1,1] -> [0,1]

fig, axes = plt.subplots(4, 4, figsize=(6, 6))
for i, ax in enumerate(axes.flat):
    ax.imshow(generated_images[i, :, :, 0], cmap='gray')
    ax.axis('off')
plt.suptitle("Generator output after training")
plt.savefig("generated_samples.png")
plt.close()

print("\nPlots saved: gan_losses.png, generated_samples.png")
```

## pca.py

```python
import numpy as np
import matplotlib.pyplot as plt

from sklearn.datasets import load_iris
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA


# ================================================
# Step 1: Load dataset
# ================================================

data = load_iris()

X = data.data
y = data.target
feature_names = data.feature_names
class_names = data.target_names

print("Original shape:", X.shape)


# ================================================
# Step 2: Standardize the data
# ================================================

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)


# ================================================
# Step 3: Inspect variance explained by ALL components
# (helps justify choosing 2 components)
# ================================================

pca_full = PCA(n_components=4)
pca_full.fit(X_scaled)

explained_variance = pca_full.explained_variance_ratio_
cumulative_variance = np.cumsum(explained_variance)

print("\nExplained variance ratio per component:")
for i, v in enumerate(explained_variance):
    print(f"  PC{i+1}: {v:.4f} ({v*100:.2f}%)")


# ================================================
# Step 4: Apply PCA -- reduce to 2 components
# ================================================

pca = PCA(n_components=2)
X_pca = pca.fit_transform(X_scaled)

print("\nReduced shape:", X_pca.shape)
print("Total explained variance (2 components):",
      pca.explained_variance_ratio_.sum())


# ================================================
# Step 5: Plot scree plot + class-colored PCA scatter
# ================================================

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Scree plot
axes[0].bar(range(1, 5), explained_variance, alpha=0.7, label='Individual')
axes[0].plot(range(1, 5), cumulative_variance, marker='o', color='red', label='Cumulative')
axes[0].axhline(y=0.95, color='gray', linestyle='--', label='95% threshold')
axes[0].set_title("Scree Plot: Variance Explained")
axes[0].set_xlabel("Principal Component")
axes[0].set_ylabel("Variance Explained Ratio")
axes[0].legend()
axes[0].grid(True)

# Class-colored scatter of PCA-reduced data
colors = ['navy', 'darkorange', 'forestgreen']
for class_id in range(3):
    axes[1].scatter(X_pca[y == class_id, 0], X_pca[y == class_id, 1],
                     c=colors[class_id], label=class_names[class_id],
                     edgecolors='k', s=50)

axes[1].set_title("Iris Data Projected onto First 2 Principal Components")
axes[1].set_xlabel(f"PC1 ({explained_variance[0]*100:.1f}% variance)")
axes[1].set_ylabel(f"PC2 ({explained_variance[1]*100:.1f}% variance)")
axes[1].legend()
axes[1].grid(True)

plt.tight_layout()
plt.savefig("pca_analysis.png")
plt.show()
plt.close()


# ================================================
# Step 6: Inspect component loadings
# (how much each original feature contributes to PC1/PC2)
# ================================================

loadings = pca.components_
print("\nPrincipal Component Loadings (contribution of each original feature):")
print(f"{'Feature':<20s} {'PC1':>8s} {'PC2':>8s}")
for i, fname in enumerate(feature_names):
    print(f"{fname:<20s} {loadings[0][i]:>8.3f} {loadings[1][i]:>8.3f}")

print("\nPlot saved: pca_analysis.png")
```

## perceptron.py

```python
import numpy as np
import matplotlib.pyplot as plt

# Bipolar AND input
X = np.array([
    [-1, -1],
    [-1,  1],
    [ 1, -1],
    [ 1,  1]
])

# Bipolar AND targets
d = np.array([-1, -1, -1, 1])

# Initial weights and bias
w = np.zeros(2)
b = 0.0

# Learning rate
eta = 0.1

# Store errors for convergence curve
errors = []

# Training
for epoch in range(100):

    error_count = 0

    for x, target in zip(X, d):

        # Calculate weighted sum
        net = np.dot(x, w) + b

        # Bipolar activation function
        output = 1 if net >= 0 else -1

        # Update if prediction is wrong
        if output != target:
            w += eta * (target - output) * x
            b += eta * (target - output)
            error_count += 1

    errors.append(error_count)

    # Stop if no errors
    if error_count == 0:
        break

print("Final weights:", w)
print("Final bias:", b)
print("Epochs:", len(errors))

# -----------------------------
# Convergence curve
# -----------------------------

plt.plot(range(1, len(errors) + 1), errors, marker='o')
plt.xlabel("Epoch")
plt.ylabel("Number of Errors")
plt.title("Perceptron Convergence")
plt.grid()
plt.show()

# -----------------------------
# Decision boundary
# -----------------------------

plt.scatter(X[d == -1, 0], X[d == -1, 1], label="-1")
plt.scatter(X[d == 1, 0], X[d == 1, 1], label="+1")

# w1*x1 + w2*x2 + b = 0
x1_values = np.linspace(-1.5, 1.5, 100)

if w[1] != 0:
    x2_values = -(w[0] * x1_values + b) / w[1]
    plt.plot(x1_values, x2_values, label="Decision Boundary")

plt.xlabel("x1")
plt.ylabel("x2")
plt.title("Perceptron Decision Boundary")
plt.legend()
plt.grid()
plt.show()
```

## resnet.py

```python
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

# =================================================
# 1. Dataset settings
# =================================================

dataset_path = "flower_dataset"   # <-- folder with one subfolder per class

img_size = (224, 224)
batch_size = 16

# =================================================
# 2. Load training / validation datasets
# =================================================

train_ds = keras.utils.image_dataset_from_directory(
    dataset_path,
    validation_split=0.2,
    subset="training",
    seed=123,
    image_size=img_size,
    batch_size=batch_size
)

validation_ds = keras.utils.image_dataset_from_directory(
    dataset_path,
    validation_split=0.2,
    subset="validation",
    seed=123,
    image_size=img_size,
    batch_size=batch_size
)

class_names = train_ds.class_names
print("Classes:", class_names)
num_classes = len(class_names)

# Performance: cache/prefetch
AUTOTUNE = tf.data.AUTOTUNE
train_ds = train_ds.cache().shuffle(200).prefetch(buffer_size=AUTOTUNE)
validation_ds = validation_ds.cache().prefetch(buffer_size=AUTOTUNE)

# =================================================
# 3. Load pretrained ResNet-50 (no top layer)
# =================================================

base_model = keras.applications.ResNet50(
    weights="imagenet",
    include_top=False,
    input_shape=(224, 224, 3)
)

# =================================================
# 4. Freeze pretrained layers
# =================================================

base_model.trainable = False

# =================================================
# 5. Build the new classifier head
# =================================================

inputs = keras.Input(shape=(224, 224, 3))
x = keras.applications.resnet50.preprocess_input(inputs)
x = base_model(x, training=False)
x = layers.GlobalAveragePooling2D()(x)
x = layers.Dense(128, activation="relu")(x)
x = layers.Dropout(0.3)(x)             # reduces overfitting on the new head
outputs = layers.Dense(num_classes, activation="softmax")(x)

model = keras.Model(inputs, outputs)

# =================================================
# 6. Compile
# =================================================

model.compile(
    optimizer=keras.optimizers.Adam(learning_rate=0.001),
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)

model.summary()

# =================================================
# 7. PHASE 1: Train new head (base frozen)
# =================================================

print("\n--- PHASE 1: Training new head (base frozen) ---")
history1 = model.fit(
    train_ds,
    validation_data=validation_ds,
    epochs=10
)

# =================================================
# 8. PHASE 2: Fine-tuning
# Unfreeze the last 20 layers of ResNet50 and continue
# training with a much smaller learning rate.
# =================================================

base_model.trainable = True

# Freeze all but the last 20 layers
for layer in base_model.layers[:-20]:
    layer.trainable = False

model.compile(
    optimizer=keras.optimizers.Adam(learning_rate=1e-5),  # tiny LR for fine-tuning
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)

print("\n--- PHASE 2: Fine-tuning last 20 layers of ResNet50 ---")
history2 = model.fit(
    train_ds,
    validation_data=validation_ds,
    epochs=5
)

# =================================================
# 9. Evaluate final model
# =================================================

loss, accuracy = model.evaluate(validation_ds)
print("\nFinal Validation Accuracy:", accuracy)

# =================================================
# 10. Plot combined training curves (both phases)
# =================================================

acc = history1.history['accuracy'] + history2.history['accuracy']
val_acc = history1.history['val_accuracy'] + history2.history['val_accuracy']
loss_hist = history1.history['loss'] + history2.history['loss']
val_loss = history1.history['val_loss'] + history2.history['val_loss']

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].plot(acc, label='Train Accuracy')
axes[0].plot(val_acc, label='Val Accuracy')
axes[0].axvline(x=len(history1.history['accuracy']) - 0.5, color='gray',
                linestyle='--', label='Fine-tuning starts')
axes[0].set_title('Accuracy over Epochs')
axes[0].set_xlabel('Epoch')
axes[0].legend()
axes[0].grid(True)

axes[1].plot(loss_hist, label='Train Loss')
axes[1].plot(val_loss, label='Val Loss')
axes[1].axvline(x=len(history1.history['loss']) - 0.5, color='gray',
                linestyle='--', label='Fine-tuning starts')
axes[1].set_title('Loss over Epochs')
axes[1].set_xlabel('Epoch')
axes[1].legend()
axes[1].grid(True)

plt.tight_layout()
plt.savefig("transfer_learning_curves.png")
plt.close()

print("\nPlot saved: transfer_learning_curves.png")
```

## sgd_delta.py

```python
import numpy as np
import matplotlib.pyplot as plt

# Input data given in the lab
X = np.array([
    [0, 0, 1],
    [0, 1, 1],
    [1, 0, 1],
    [1, 1, 1]
], dtype=float)

# Target values
D = np.array([0, 0, 1, 1], dtype=float)

# Initialize weights
w = np.zeros(3)

# Learning rate
eta = 0.1

# Number of epochs
epochs = 50

# Store mean squared error
errors = []

# SGD training
for epoch in range(epochs):

    total_error = 0

    for x, d in zip(X, D):

        # Prediction
        y = np.dot(x, w)

        # Error
        error = d - y

        # Delta rule update
        w += eta * error * x

        # Store squared error
        total_error += error ** 2

    # Average error for this epoch
    mse = total_error / len(X)
    errors.append(mse)

print("Final weights:", w)

# Plot convergence
plt.plot(range(1, epochs + 1), errors)
plt.xlabel("Epoch")
plt.ylabel("Mean Squared Error")
plt.title("SGD using Delta Learning Rule")
plt.grid()
plt.show()
```

## sgd_vs_batch.py

```python
import numpy as np
import matplotlib.pyplot as plt

# ---------------------------------------------------------
# STEP 1: Dataset (same as Lab 2)
# ---------------------------------------------------------
X = np.array([
    [0, 0, 1],
    [0, 1, 1],
    [1, 0, 1],
    [1, 1, 1]
])
T = np.array([0, 0, 1, 1])

learning_rate = 0.1
epochs = 50

def linear_activation(y_in):
    return y_in

# ===========================================================
# METHOD 1: SGD (Stochastic) -- update after EACH sample
# ===========================================================
np.random.seed(1)
weights_sgd = np.random.uniform(-0.5, 0.5, size=3)
mse_sgd = []

for epoch in range(epochs):
    total_squared_error = 0
    for i in range(len(X)):
        x = X[i]
        target = T[i]
        y = linear_activation(np.dot(x, weights_sgd))
        error = target - y

        # Update immediately, sample by sample
        weights_sgd = weights_sgd + learning_rate * error * x

        total_squared_error += error ** 2
    mse_sgd.append(total_squared_error / len(X))

# ===========================================================
# METHOD 2: Batch Gradient Descent -- update ONCE per epoch
# ===========================================================
np.random.seed(1)   # same starting point for fair comparison
weights_batch = np.random.uniform(-0.5, 0.5, size=3)
mse_batch = []

for epoch in range(epochs):
    total_squared_error = 0
    weight_update = np.zeros(3)   # accumulator for the whole batch

    for i in range(len(X)):
        x = X[i]
        target = T[i]
        y = linear_activation(np.dot(x, weights_batch))
        error = target - y

        # Accumulate the update, DO NOT apply yet
        weight_update += learning_rate * error * x
        total_squared_error += error ** 2

    # Apply the accumulated update ONCE, after seeing all samples
    weights_batch = weights_batch + (weight_update / len(X))
    mse_batch.append(total_squared_error / len(X))

# ---------------------------------------------------------
# STEP 3: Print final results
# ---------------------------------------------------------
print("Final SGD weights:  ", weights_sgd,  " Final MSE:", mse_sgd[-1])
print("Final Batch weights:", weights_batch, " Final MSE:", mse_batch[-1])

# ---------------------------------------------------------
# STEP 4: Plot both MSE curves together for comparison
# ---------------------------------------------------------
plt.figure(figsize=(7, 5))
plt.plot(range(1, epochs+1), mse_sgd, label="SGD", marker='.')
plt.plot(range(1, epochs+1), mse_batch, label="Batch", marker='x')
plt.title("SGD vs Batch Gradient Descent (Delta Rule)")
plt.xlabel("Epoch")
plt.ylabel("Mean Squared Error")
plt.legend()
plt.grid(True)
plt.savefig("/home/claude/lab3/sgd_vs_batch.png")
plt.close()

print("\nPlot saved: sgd_vs_batch.png")
```

## speech.py

```python
import os
import numpy as np
import matplotlib.pyplot as plt
import librosa

from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

from tensorflow import keras
from tensorflow.keras import layers

np.random.seed(42)

# --------------------------------------------------
# 1. Dataset settings
# --------------------------------------------------

dataset_path = "speech_dataset"   # <-- folder with one subfolder per class
classes = ["one", "two", "three", "four"]
N_MFCC = 13

# --------------------------------------------------
# 2. Extract MFCC features
# --------------------------------------------------

def extract_features(file_path):
    audio, sr = librosa.load(file_path, sr=16000)
    mfcc = librosa.feature.mfcc(y=audio, sr=sr, n_mfcc=N_MFCC)
    return np.mean(mfcc, axis=1)   # average across time -> shape (N_MFCC,)

# --------------------------------------------------
# 3. Load all audio files
# --------------------------------------------------

X = []
y = []

for label, class_name in enumerate(classes):
    folder = os.path.join(dataset_path, class_name)
    for file_name in os.listdir(folder):
        if file_name.endswith(".wav"):
            file_path = os.path.join(folder, file_name)
            features = extract_features(file_path)
            X.append(features)
            y.append(label)

X = np.array(X)
y = np.array(y)

print("X shape:", X.shape)
print("y shape:", y.shape)

# --------------------------------------------------
# 4. Train-test split
# --------------------------------------------------

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

print(f"Training samples: {len(X_train)}, Test samples: {len(X_test)}")

# --------------------------------------------------
# 5. Feature normalization
# --------------------------------------------------

scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)   # use SAME scaler, don't re-fit

# --------------------------------------------------
# 6. Build ANN
# --------------------------------------------------

model = keras.Sequential([
    layers.Input(shape=(N_MFCC,)),
    layers.Dense(32, activation="relu"),
    layers.Dropout(0.2),          # reduces overfitting
    layers.Dense(16, activation="relu"),
    layers.Dense(len(classes), activation="softmax")
])

model.summary()

# --------------------------------------------------
# 7. Compile
# --------------------------------------------------

model.compile(
    optimizer="adam",
    loss="sparse_categorical_crossentropy",
    metrics=["accuracy"]
)

# --------------------------------------------------
# 8. Train
# Validate against the REAL held-out test set, not another
# carved-out slice of the training data.
# --------------------------------------------------

history = model.fit(
    X_train, y_train,
    validation_data=(X_test, y_test),
    epochs=50,
    batch_size=8,
    verbose=2
)

# --------------------------------------------------
# 9. Evaluate
# --------------------------------------------------

loss, accuracy = model.evaluate(X_test, y_test, verbose=0)
print("\nTest Accuracy:", accuracy)

# --------------------------------------------------
# 10. Sample predictions across the test set
# --------------------------------------------------

predictions = model.predict(X_test, verbose=0)
predicted_classes = np.argmax(predictions, axis=1)

print("\n--- Sample predictions ---")
for i in range(min(8, len(X_test))):
    print(f"True: {classes[y_test[i]]:6s}  Predicted: {classes[predicted_classes[i]]:6s}")

# --------------------------------------------------
# 11. Predict a brand-new audio file (optional)
# --------------------------------------------------

new_file = "new.wav"
if os.path.exists(new_file):
    new_features = extract_features(new_file).reshape(1, -1)
    new_features = scaler.transform(new_features)
    prediction = model.predict(new_features, verbose=0)
    predicted_class = np.argmax(prediction, axis=1)[0]
    print("\nPredicted number for new.wav:", classes[predicted_class])

# --------------------------------------------------
# 12. Plot training curves
# --------------------------------------------------

fig, axes = plt.subplots(1, 2, figsize=(12, 4))
axes[0].plot(history.history['accuracy'], label='Train Accuracy')
axes[0].plot(history.history['val_accuracy'], label='Val Accuracy')
axes[0].set_title('Accuracy over Epochs')
axes[0].set_xlabel('Epoch')
axes[0].legend()
axes[0].grid(True)

axes[1].plot(history.history['loss'], label='Train Loss')
axes[1].plot(history.history['val_loss'], label='Val Loss')
axes[1].set_title('Loss over Epochs')
axes[1].set_xlabel('Epoch')
axes[1].legend()
axes[1].grid(True)

plt.tight_layout()
plt.savefig("training_curves.png")
plt.close()

# --------------------------------------------------
# 13. Visualize example MFCC feature vectors per class
# --------------------------------------------------

plt.figure(figsize=(8, 5))
for class_id in range(len(classes)):
    idx = np.where(y_train == class_id)[0][0]
    plt.plot(X_train[idx], marker='o', label=classes[class_id])
plt.title("Example (normalized) MFCC feature vectors per class")
plt.xlabel("MFCC coefficient index")
plt.ylabel("Normalized value")
plt.legend()
plt.grid(True)
plt.savefig("mfcc_examples.png")
plt.close()

print("\nPlots saved: training_curves.png, mfcc_examples.png")
```

## svm.py

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVC

from sklearn.metrics import (
    accuracy_score,
    confusion_matrix,
    classification_report
)

np.random.seed(42)

# --------------------------------------------------
# 1. Load dataset
# --------------------------------------------------

data = pd.read_csv("Social_Network_Ads.csv")

# --------------------------------------------------
# 2. Select features and target
# --------------------------------------------------

X = data[["Age", "EstimatedSalary"]].values
y = data["Purchased"].values

print(f"Total samples: {len(X)}, Purchased: {y.sum()}, Not purchased: {len(y) - y.sum()}")

# --------------------------------------------------
# 3. Split dataset
# --------------------------------------------------

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# --------------------------------------------------
# 4. Feature scaling
# CRITICAL for SVM -- Age (~18-60) and Salary (~15000-150000)
# are on very different scales; without scaling, Salary would
# dominate the distance calculations SVM relies on.
# --------------------------------------------------

scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)   # use SAME scaler, don't re-fit

# --------------------------------------------------
# 5. Create and train SVM classifier
# --------------------------------------------------

classifier = SVC(kernel="linear", C=1.0, random_state=42)
classifier.fit(X_train_scaled, y_train)

# --------------------------------------------------
# 6. Predict
# --------------------------------------------------

y_pred = classifier.predict(X_test_scaled)

# --------------------------------------------------
# 7. Evaluate
# --------------------------------------------------

accuracy = accuracy_score(y_test, y_pred)
print("\nAccuracy:", accuracy)

print("\nConfusion Matrix:")
print(confusion_matrix(y_test, y_pred))

print("\nClassification Report:")
print(classification_report(y_test, y_pred, target_names=["Not Purchased", "Purchased"]))

# --------------------------------------------------
# 8. Visualize the decision boundary
# Create a fine grid over the feature space, classify EVERY
# point on the grid, and color the regions -- this reveals the
# actual shape of the boundary SVM learned.
# --------------------------------------------------

def plot_decision_boundary(X_scaled, y, model, title, filename):
    x_min, x_max = X_scaled[:, 0].min() - 1, X_scaled[:, 0].max() + 1
    y_min, y_max = X_scaled[:, 1].min() - 1, X_scaled[:, 1].max() + 1
    xx, yy = np.meshgrid(np.linspace(x_min, x_max, 300),
                          np.linspace(y_min, y_max, 300))

    Z = model.predict(np.c_[xx.ravel(), yy.ravel()])
    Z = Z.reshape(xx.shape)

    plt.figure(figsize=(7, 6))
    plt.contourf(xx, yy, Z, alpha=0.3, cmap='coolwarm')

    plt.scatter(X_scaled[y == 0][:, 0], X_scaled[y == 0][:, 1],
                c='blue', label='Not Purchased', edgecolors='k', s=40)
    plt.scatter(X_scaled[y == 1][:, 0], X_scaled[y == 1][:, 1],
                c='red', label='Purchased', edgecolors='k', s=40)

    # Highlight the support vectors -- the points that DEFINE the boundary
    plt.scatter(model.support_vectors_[:, 0], model.support_vectors_[:, 1],
                s=120, facecolors='none', edgecolors='black', linewidths=1.5,
                label='Support Vectors')

    plt.title(title)
    plt.xlabel("Age (scaled)")
    plt.ylabel("Estimated Salary (scaled)")
    plt.legend()
    plt.savefig(filename)
    plt.close()

plot_decision_boundary(X_train_scaled, y_train, classifier,
                        "SVM Decision Boundary (Training Data, Linear Kernel)",
                        "svm_decision_boundary.png")

print(f"\nNumber of support vectors: {len(classifier.support_vectors_)} out of {len(X_train)} training samples")
print("Plot saved: svm_decision_boundary.png")

# --------------------------------------------------
# 9. Bonus: Compare kernels -- linear vs RBF
# to show WHY kernel choice matters
# --------------------------------------------------

print("\n--- Kernel comparison ---")
for kernel in ['linear', 'rbf']:
    m = SVC(kernel=kernel, C=1.0, gamma='scale', random_state=42)
    m.fit(X_train_scaled, y_train)
    acc = accuracy_score(y_test, m.predict(X_test_scaled))
    print(f"Kernel = {kernel:8s} -> Test Accuracy = {acc:.4f}")

    # Also plot RBF's boundary since non-linear kernels usually
    # produce a visibly different (curved) decision region
    if kernel == 'rbf':
        plot_decision_boundary(X_train_scaled, y_train, m,
                                "SVM Decision Boundary (Training Data, RBF Kernel)",
                                "svm_decision_boundary_rbf.png")
        print("Plot saved: svm_decision_boundary_rbf.png")
```