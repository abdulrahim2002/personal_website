---
date: '2025-12-07T13:17:50+01:00'
draft: false
author: 'Abdul'
title: 'How to Build Neural Networks From Scratch'
---

# 🎓 **PyTorch CNN Explained from Scratch - Step by Step**

I'll explain everything from the absolute basics. No prior PyTorch knowledge required!

## 📚 **Part 1: The Absolute Basics - Understanding the Pieces**

### **1.1 What is a CNN?**
A **Convolutional Neural Network (CNN)** is like a detective that:
1. **Scans** an image with small filters to find patterns (edges, textures)
2. **Combines** these patterns to recognize objects
3. **Learns** which patterns are important for classification

**Analogy**: Imagine looking for a face in a crowd:
- First, you look for eyes (small patterns)
- Then, you combine eyes+nose+mouth to find faces
- Finally, you recognize whose face it is

### **1.2 Core Components**

#### **1.2.1 Images in Computers**
```python
# An image is just a 3D array of numbers
# Shape: [Channels, Height, Width]
# Channels: 1 for grayscale, 3 for color (RGB)

# Example: A 28x28 grayscale MNIST digit
image = torch.randn(1, 28, 28)  # 1 channel, 28 height, 28 width
print(f"Image shape: {image.shape}")
```

#### **1.2.2 Convolution Operation**
```python
# A filter is like a magnifying glass that slides over the image
# It multiplies numbers and sums them up

# Example: Edge detection filter
filter_3x3 = torch.tensor([
    [-1, -1, -1],
    [-1,  8, -1],
    [-1, -1, -1]
])

# When this slides over an image:
# It gives high values where there are edges (sudden changes)
# It gives low values where the image is smooth
```

### **1.3 PyTorch Basics - The Building Blocks**

```python
import torch
import torch.nn as nn  # Neural network modules
import torch.nn.functional as F  # Functions like ReLU, softmax

# PyTorch works with TENSORS (fancy arrays)
x = torch.tensor([[1, 2], [3, 4]])  # Create a 2x2 tensor
print(f"Tensor shape: {x.shape}")
print(f"Tensor values:\n{x}")

# Neural Network Layer
layer = nn.Linear(in_features=10, out_features=5)
# This creates: output = weight * input + bias
# weight shape: [5, 10], bias shape: [5]
```

## 🏗️ **Part 2: Building a CNN Layer by Layer**

Let's build the simplest possible CNN and understand each part.

### **2.1 The Complete Simple CNN - Explained Line by Line**

```python
class SimpleCNN(nn.Module):
    def __init__(self):
        super(SimpleCNN, self).__init__()  # Must call this first

        # ----- LAYER 1: First Convolution -----
        # What it does: Looks for simple patterns (edges, corners)
        self.conv1 = nn.Conv2d(
            in_channels=1,      # Input has 1 channel (grayscale)
            out_channels=4,     # We want 4 different filters
            kernel_size=3,      # Filter is 3x3 pixels
            padding=1           # Add 1 pixel border to keep same size
        )
        # After this layer: [batch, 1, 28, 28] → [batch, 4, 28, 28]

        # ----- POOLING LAYER -----
        # What it does: Makes image smaller, keeps only important info
        self.pool = nn.MaxPool2d(
            kernel_size=2,  # Look at 2x2 blocks
            stride=2        # Move 2 pixels each time
        )
        # After pooling: [batch, 4, 28, 28] → [batch, 4, 14, 14]
        # Takes maximum value from each 2x2 block (downsamples)

        # ----- LAYER 2: Second Convolution -----
        # What it does: Looks for more complex patterns (combinations of edges)
        self.conv2 = nn.Conv2d(
            in_channels=4,      # Input has 4 channels (from previous layer)
            out_channels=8,     # We want 8 different filters
            kernel_size=3,      # Filter is 3x3
            padding=1           # Keep same size
        )
        # After: [batch, 4, 14, 14] → [batch, 8, 14, 14]
        # After pooling: [batch, 8, 14, 14] → [batch, 8, 7, 7]

        # ----- FLATTEN LAYER (not really a layer, just reshaping) -----
        # What it does: Converts 3D feature maps to 1D vector
        # [batch, 8, 7, 7] → [batch, 8 * 7 * 7] = [batch, 392]

        # ----- FULLY CONNECTED LAYER 1 -----
        # What it does: Combines all features to make decisions
        self.fc1 = nn.Linear(
            in_features=8 * 7 * 7,  # 392 features from conv layers
            out_features=64          # 64 neurons in this layer
        )

        # ----- FINAL CLASSIFICATION LAYER -----
        # What it does: Decides which digit it is (0-9)
        self.fc2 = nn.Linear(
            in_features=64,     # From previous layer
            out_features=10     # 10 classes (digits 0-9)
        )

    def forward(self, x):
        """This defines HOW data flows through the network"""
        # x shape starts as: [batch_size, 1, 28, 28]

        # Step 1: First convolution + activation + pooling
        x = self.pool(F.relu(self.conv1(x)))
        # ReLU: Makes negative values 0, keeps positives
        # Example: ReLU([-2, 0, 5]) = [0, 0, 5]

        # Step 2: Second convolution + activation + pooling
        x = self.pool(F.relu(self.conv2(x)))

        # Step 3: Flatten (3D → 1D)
        x = x.view(-1, 8 * 7 * 7)  # -1 means "figure out batch size automatically"

        # Step 4: First fully connected layer + activation
        x = F.relu(self.fc1(x))

        # Step 5: Final classification (no activation here - will add softmax later)
        x = self.fc2(x)

        return x
```

### **2.2 Let's Visualize What Happens Step by Step**

```python
# Create a mini network to see shapes
mini_cnn = SimpleCNN()

# Create a fake "batch" of 2 images
batch_size = 2
fake_images = torch.randn(batch_size, 1, 28, 28)  # [2, 1, 28, 28]
print(f"Input shape: {fake_images.shape}")

# Let's trace through manually
print("\n=== Tracing the CNN ===")

# After conv1
x = mini_cnn.conv1(fake_images)
print(f"After conv1: {x.shape}")  # [2, 4, 28, 28]

# After ReLU (just changes values, not shape)
x = F.relu(x)
print(f"After ReLU: {x.shape}")  # Still [2, 4, 28, 28]

# After pooling (makes it smaller)
x = mini_cnn.pool(x)
print(f"After pool: {x.shape}")  # [2, 4, 14, 14] - HALF the size!

# After conv2
x = mini_cnn.conv2(x)
print(f"After conv2: {x.shape}")  # [2, 8, 14, 14]

# After pooling again
x = mini_cnn.pool(x)
print(f"After pool2: {x.shape}")  # [2, 8, 7, 7]

# Flatten
x = x.view(batch_size, -1)  # -1 means "figure out this dimension"
print(f"After flatten: {x.shape}")  # [2, 392] (8*7*7 = 392)

# Through fc1
x = mini_cnn.fc1(x)
print(f"After fc1: {x.shape}")  # [2, 64]

# Through fc2 (final output)
x = mini_cnn.fc2(x)
print(f"After fc2 (final): {x.shape}")  # [2, 10]
```

## 🎯 **Part 3: The Complete Training Process - Explained**

### **3.1 What Does Training Actually Mean?**

**Training = Adjusting the numbers (weights) in the filters to make better predictions**

Think of it like tuning a radio:
- Initial random weights = static noise
- Training = turning the dial to get clear signal
- Good weights = clear reception

### **3.2 The Training Loop - Step by Step**

```python
# Simplified training example
def simple_train():
    # 1. CREATE MODEL
    model = SimpleCNN()

    # 2. LOSS FUNCTION - Measures how wrong we are
    criterion = nn.CrossEntropyLoss()
    # What it does: Compares predictions [0.1, 0.8, 0.1] with true label [0, 1, 0]
    # Returns a single number: lower is better

    # 3. OPTIMIZER - Adjusts the weights
    optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
    # Adam: Smart gradient descent
    # lr=0.001: How big of steps to take when adjusting weights

    # 4. CREATE FAKE DATA (for demonstration)
    batch_size = 4
    images = torch.randn(batch_size, 1, 28, 28)  # Random images
    labels = torch.tensor([3, 7, 2, 9])  # Random labels (digits 0-9)

    print(f"Images shape: {images.shape}")
    print(f"Labels: {labels}")

    # 5. ONE TRAINING STEP - Explained
    print("\n=== ONE TRAINING STEP ===")

    # Step A: Zero gradients (very important!)
    optimizer.zero_grad()
    # Why: PyTorch accumulates gradients. We need to reset them each step.

    # Step B: Forward pass (make prediction)
    outputs = model(images)
    print(f"Raw outputs shape: {outputs.shape}")  # [4, 10]
    print(f"Sample output: {outputs[0]}")

    # Step C: Calculate loss (how wrong are we?)
    loss = criterion(outputs, labels)
    print(f"Loss: {loss.item():.4f}")

    # Step D: Backward pass (figure out what to adjust)
    loss.backward()
    # This calculates gradients: ∂loss/∂weight for EVERY weight

    # Step E: Update weights (actually adjust them)
    optimizer.step()
    # This does: weight = weight - lr * gradient

    print("Weights have been updated! Model learned a little.")

    return model

# Run it
model = simple_train()
```

### **3.3 What Happens During Backpropagation?**

```python
# Visual explanation of gradients
def explain_gradients():
    model = SimpleCNN()

    # Get one weight from the first convolution filter
    weight_before = model.conv1.weight[0, 0, 0, 0].item()
    print(f"One weight before training: {weight_before:.6f}")

    # Do one training step
    optimizer = torch.optim.Adam(model.parameters(), lr=0.01)
    criterion = nn.CrossEntropyLoss()

    # Fake training
    images = torch.randn(1, 1, 28, 28)
    labels = torch.tensor([5])

    optimizer.zero_grad()
    outputs = model(images)
    loss = criterion(outputs, labels)
    loss.backward()

    # Look at the gradient for that weight
    gradient = model.conv1.weight.grad[0, 0, 0, 0].item()
    print(f"Gradient for that weight: {gradient:.6f}")
    print("This tells us: Should we increase or decrease this weight?")
    print(f"Positive gradient → Decrease weight")
    print(f"Negative gradient → Increase weight")

    # Update weights
    optimizer.step()

    weight_after = model.conv1.weight[0, 0, 0, 0].item()
    print(f"Same weight after training: {weight_after:.6f}")
    print(f"Change: {weight_after - weight_before:.6f}")

explain_gradients()
```

## 📊 **Part 4: Data Loading and Preparation**

### **4.1 Understanding Datasets and DataLoaders**

```python
from torch.utils.data import Dataset, DataLoader
import torchvision.transforms as transforms

# A Dataset is like a list of (image, label) pairs
# A DataLoader gives you batches of data

class SimpleDataset(Dataset):
    """Create your own dataset (for understanding)"""
    def __init__(self, num_samples=100):
        # Create fake data
        self.images = torch.randn(num_samples, 1, 28, 28)  # Random images
        self.labels = torch.randint(0, 10, (num_samples,))  # Random labels 0-9

    def __len__(self):
        return len(self.images)  # How many samples we have

    def __getitem__(self, idx):
        return self.images[idx], self.labels[idx]  # Get one sample

# Create dataset
dataset = SimpleDataset(100)
print(f"Dataset has {len(dataset)} samples")

# Create DataLoader
dataloader = DataLoader(
    dataset,
    batch_size=10,  # Give me 10 samples at a time
    shuffle=True,   # Mix them up randomly
    num_workers=0   # Don't use multiple CPU cores (for simplicity)
)

# See what a batch looks like
for batch_idx, (images, labels) in enumerate(dataloader):
    print(f"\nBatch {batch_idx}:")
    print(f"  Images shape: {images.shape}")  # [10, 1, 28, 28]
    print(f"  Labels: {labels}")              # [10]

    if batch_idx == 2:  # Just look at first 3 batches
        break
```

### **4.2 Real MNIST Data**

```python
def load_real_mnist():
    """Load actual MNIST digits"""
    transform = transforms.Compose([
        transforms.ToTensor(),  # Convert PIL image to tensor
        transforms.Normalize((0.1307,), (0.3081,))  # Standardize
    ])

    # Download MNIST
    train_dataset = torchvision.datasets.MNIST(
        root='./data',
        train=True,
        download=True,
        transform=transform
    )

    print(f"MNIST has {len(train_dataset)} training images")

    # Look at one sample
    image, label = train_dataset[0]
    print(f"\nFirst sample:")
    print(f"  Image shape: {image.shape}")  # [1, 28, 28]
    print(f"  Label: {label}")               # e.g., 5
    print(f"  Image min/max: {image.min():.3f}, {image.max():.3f}")

    # Show the image
    plt.imshow(image.squeeze(), cmap='gray')
    plt.title(f"Label: {label}")
    plt.show()

    return train_dataset

# Uncomment to run
# train_dataset = load_real_mnist()
```

## 🚀 **Part 5: Complete Working Example - Run This First!**

Here's the simplest complete CNN you can run:

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim
import matplotlib.pyplot as plt
import numpy as np

# Set random seeds for reproducibility
torch.manual_seed(42)
np.random.seed(42)

# ========== 1. DEFINE THE SIMPLEST CNN ==========
class TinyCNN(nn.Module):
    """The smallest possible CNN to understand"""
    def __init__(self):
        super().__init__()
        # Layer 1: Simple convolution
        self.conv = nn.Conv2d(1, 3, kernel_size=3, padding=1)  # 3 filters

        # Layer 2: Classification
        self.fc = nn.Linear(3 * 28 * 28, 10)  # 10 classes for digits

    def forward(self, x):
        # Apply convolution
        x = F.relu(self.conv(x))

        # Flatten
        x = x.view(x.size(0), -1)  # Flatten all dimensions except batch

        # Classify
        x = self.fc(x)
        return x

# ========== 2. CREATE FAKE DATA ==========
def create_fake_data(num_samples=100):
    """Create simple fake data for testing"""
    # Create random "images"
    images = torch.randn(num_samples, 1, 28, 28)  # Like MNIST digits

    # Create random labels (0-9)
    labels = torch.randint(0, 10, (num_samples,))

    return images, labels

# ========== 3. TRAIN FUNCTION ==========
def train_one_epoch(model, images, labels):
    """Train for one epoch (one pass through data)"""
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.SGD(model.parameters(), lr=0.01)

    # Reset gradients
    optimizer.zero_grad()

    # Forward pass
    outputs = model(images)

    # Calculate loss
    loss = criterion(outputs, labels)

    # Backward pass
    loss.backward()

    # Update weights
    optimizer.step()

    # Calculate accuracy
    _, predictions = torch.max(outputs, 1)
    correct = (predictions == labels).sum().item()
    accuracy = correct / len(labels) * 100

    return loss.item(), accuracy

# ========== 4. VISUALIZATION ==========
def visualize_predictions(model, images, labels):
    """Show what the model predicts"""
    model.eval()  # Set to evaluation mode
    with torch.no_grad():  # Don't calculate gradients (faster)
        outputs = model(images[:5])  # First 5 images
        _, predictions = torch.max(outputs, 1)

    # Plot
    fig, axes = plt.subplots(1, 5, figsize=(15, 3))
    for i in range(5):
        axes[i].imshow(images[i].squeeze(), cmap='gray')
        axes[i].set_title(f'True: {labels[i]}\nPred: {predictions[i]}')
        axes[i].axis('off')
    plt.show()

# ========== 5. MAIN EXECUTION ==========
def main():
    print("=== CNN TUTORIAL - RUN ME! ===")
    print("\n1. Creating model...")
    model = TinyCNN()
    print(f"Model created! It has:")
    print(f"  - Conv layer: 3 filters of size 3x3")
    print(f"  - FC layer: {3*28*28} inputs → 10 outputs")

    print("\n2. Creating fake data...")
    images, labels = create_fake_data(50)
    print(f"  Created {len(images)} fake images")
    print(f"  Image shape: {images[0].shape}")
    print(f"  Sample labels: {labels[:5]}")

    print("\n3. Training for 5 epochs...")
    for epoch in range(5):
        loss, acc = train_one_epoch(model, images, labels)
        print(f"  Epoch {epoch+1}: Loss = {loss:.4f}, Accuracy = {acc:.1f}%")

    print("\n4. Making predictions...")
    visualize_predictions(model, images, labels)

    print("\n5. Examining learned filters...")
    filters = model.conv.weight.detach()
    print(f"  Filter shape: {filters.shape}")  # [3, 1, 3, 3]
    print(f"  First filter values:\n{filters[0]}")

    print("\n=== DONE! ===")
    print("\nWhat we did:")
    print("1. Created a tiny CNN with 1 conv layer + 1 fc layer")
    print("2. Created fake image data")
    print("3. Trained it for 5 epochs")
    print("4. Visualized predictions")
    print("5. Looked at learned filters")

# ========== RUN IT! ==========
if __name__ == "__main__":
    main()
```

## 🎯 **Part 6: Understanding Each Component Through Analogies**

### **6.1 The CNN as a Factory Assembly Line**

```
INPUT IMAGE → [CONV] → [RELU] → [POOL] → [CONV] → [RELU] → [POOL] → [FC] → OUTPUT
    ↓           ↓         ↓        ↓        ↓         ↓        ↓       ↓       ↓
  Raw       Find      Keep     Keep     Find      Keep     Keep    Make   Final
Material  Patterns  Positives Important Patterns Positives Important Decision Classification
```

### **6.2 What Each Layer Learns**

```python
# Let's visualize what different layers might learn
def what_layers_learn():
    print("First conv layer might learn:")
    print("  - Horizontal edges")
    print("  - Vertical edges")
    print("  - Diagonal edges")
    print("  - Blobs/corners")

    print("\nSecond conv layer might learn:")
    print("  - Combinations of edges → corners")
    print("  - Simple shapes (circles, curves)")
    print("  - Texture patterns")

    print("\nFully connected layers learn:")
    print("  - 'If I see these patterns, it's probably a 0'")
    print("  - 'Circles at top + vertical lines = 8'")
    print("  - 'One vertical line = 1'")
```

### **6.3 Common Questions Answered**

**Q: Why use multiple conv layers?**
A: First layer sees pixels, second layer sees edges, third layer sees shapes, fourth layer sees objects.

**Q: What does ReLU do?**
A: It says "ignore negative evidence, only pay attention to positive evidence."

**Q: Why pooling?**
A: Makes the network care less about exact position. A "9" is still a "9" whether it's slightly left or right.

**Q: How many filters should I use?**
A: Start small (like 8-16), increase if needed. More filters = can detect more patterns, but slower.

## 📝 **Part 7: Your Turn - Exercises to Understand Better**

```python
# EXERCISE 1: Modify the TinyCNN
# Try changing these and see what happens:
# 1. Change number of filters from 3 to 8
# 2. Change kernel_size from 3 to 5
# 3. Remove the ReLU activation
# 4. Add a second conv layer

# EXERCISE 2: Count parameters
def count_parameters(model):
    total = sum(p.numel() for p in model.parameters())
    trainable = sum(p.numel() for p in model.parameters() if p.requires_grad)
    print(f"Total parameters: {total:,}")
    print(f"Trainable parameters: {trainable:,}")
    return total

# EXERCISE 3: Create your own filter
def create_edge_filter():
    """Manually create a horizontal edge detector"""
    filter = torch.tensor([
        [1, 1, 1],
        [0, 0, 0],
        [-1, -1, -1]
    ]).float()

    # Apply it to an image manually
    image = torch.randn(1, 1, 5, 5)

    # Convolve manually
    output = torch.zeros(3, 3)
    for i in range(3):
        for j in range(3):
            patch = image[0, 0, i:i+3, j:j+3]
            output[i, j] = torch.sum(patch * filter)

    print(f"Filter:\n{filter}")
    print(f"Output:\n{output}")
```

## 🚀 **Next Steps After Understanding**

1. **Run the complete example above** - it works!
2. **Modify one thing at a time** and see what happens
3. **Try real MNIST data** instead of fake data
4. **Add more layers** gradually
5. **Experiment with hyperparameters** (learning rate, batch size)

## 💡 **Quick Start - Just Run This:**

Copy and run this minimal example:

```python
# MINIMAL WORKING EXAMPLE - COPY AND RUN!
import torch
import torch.nn as nn
import torch.nn.functional as F

class SimpleCNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(1, 4, 3, padding=1)  # 4 filters
        self.pool = nn.MaxPool2d(2, 2)
        self.fc = nn.Linear(4*14*14, 10)  # 10 classes

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))  # Conv → ReLU → Pool
        x = x.view(x.size(0), -1)  # Flatten
        x = self.fc(x)  # Classify
        return x

# Create and test
model = SimpleCNN()
test_input = torch.randn(2, 1, 28, 28)  # Batch of 2 images
output = model(test_input)
print(f"Input shape: {test_input.shape}")
print(f"Output shape: {output.shape}")  # Should be [2, 10]
print("Each output has 10 numbers - probabilities for digits 0-9")
```
