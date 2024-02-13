---
title: "Fine-Tuning a Pre-Trained ResNet-18 Model for Image Classification with PyTorch"
date: 2023-03-12T10:38:50+02:00
draft: false
description: "A step-by-step guide on how to fine-tune a pre-trained ResNet-18 model for image classification on a domain-specific problem using PyTorch."
tags: ["Image Classification", "Fine-Tuning", "PyTorch"]
ShowToc: true
ShowBreadCrumbs: false
---

Fine-tuning a pre-trained model for an image classification task on a domain-specific problem can significantly reduce the time and computational resources required for training a deep neural network from scratch. In this tutorial, I will walk through the steps of fine-tuning a pre-trained ResNet-18 model on a custom dataset using PyTorch.

## Step 1: Load the pre-trained model

let's start by loading the pre-trained ResNet-18 model from the PyTorch model zoo. The PyTorch model zoo provides a collection of pre-trained models that can be used for various computer vision tasks.

```python
import torch
import torchvision.models as models

# Load the pre-trained ResNet-18 model
model = models.resnet18(pretrained=True)
```

## Step 2: Freeze the pre-trained layers

Next, freeze the pre-trained layers of the ResNet-18 model so that we can only train the last few layers for our specific task. This is done to prevent the weights of the pre-trained layers from being updated during training.

```python
# Freeze all the pre-trained layers
for param in model.parameters():
    param.requires_grad = False
```

## Step 3: Modify the last layer

The last layer of the pre-trained ResNet-18 model is a fully connected layer that outputs a 1000-dimensional vector. Since the main idea here is to use this model for a domain-specific image classification task, we need to modify the last layer to output the number of classes in our dataset.

```python
# Modify the last layer of the model
num_classes = 10 # replace with the number of classes in your dataset
model.fc = torch.nn.Linear(model.fc.in_features, num_classes)
```

## Step 4: Load the custom dataset

Next, load the custom dataset that you want to use for training the fine-tuned model. In this example, I will assume that the dataset is organized in the following directory structure:

```python
custom_dataset/
├── train/
│   ├── class1/
│   ├── class2/
│   ├── ...
├── val/
│   ├── class1/
│   ├── class2/
│   ├── ...
```

I will use the PyTorch `ImageFolder` dataset class to load the dataset.

```python
from torchvision.datasets import ImageFolder
from torchvision.transforms import transforms

# Define the transformations to apply to the images
transform = transforms.Compose([
    transforms.Resize(256),
    transforms.CenterCrop(224),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])
])

# Load the train and validation datasets
train_dataset = ImageFolder('custom_dataset/train', transform=transform)
val_dataset = ImageFolder('custom_dataset/val', transform=transform)
```

## Step 5: Define the loss function and optimizer

I will use the cross-entropy loss function and the stochastic gradient descent (SGD) optimizer for training the fine-tuned model.

```python
# Define the loss function and optimizer
criterion = torch.nn.CrossEntropyLoss()
optimizer = torch.optim.SGD(model.fc.parameters(), lr=0.001, momentum=0.9)
```

## Step 6: Train the model

Finally, train the fine-tuned model on the custom dataset using PyTorch's `DataLoader` and `TrainLoader` utilities.

```python
from torch.utils.data import DataLoader

# Create data loaders for the train and validation datasets
train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True)
val_loader = DataLoader(val_dataset, batch_size=32, shuffle=False)
```

Next, define a function to train the fine-tuned model for a specified number of epochs.

```python
def train(model, train_loader, val_loader, criterion, optimizer, num_epochs):
    # Train the model for the specified number of epochs
    for epoch in range(num_epochs):
        # Set the model to train mode
        model.train()

        # Initialize the running loss and accuracy
        running_loss = 0.0
        running_corrects = 0

        # Iterate over the batches of the train loader
        for inputs, labels in train_loader:
            # Move the inputs and labels to the device
            inputs = inputs.to(device)
            labels = labels.to(device)

            # Zero the optimizer gradients
            optimizer.zero_grad()

            # Forward pass
            outputs = model(inputs)
            _, preds = torch.max(outputs, 1)
            loss = criterion(outputs, labels)

            # Backward pass and optimizer step
            loss.backward()
            optimizer.step()

            # Update the running loss and accuracy
            running_loss += loss.item() * inputs.size(0)
            running_corrects += torch.sum(preds == labels.data)

        # Calculate the train loss and accuracy
        train_loss = running_loss / len(train_dataset)
        train_acc = running_corrects.double() / len(train_dataset)

        # Set the model to evaluation mode
        model.eval()

        # Initialize the running loss and accuracy
        running_loss = 0.0
        running_corrects = 0

        # Iterate over the batches of the validation loader
        with torch.no_grad():
            for inputs, labels in val_loader:
                # Move the inputs and labels to the device
                inputs = inputs.to(device)
                labels = labels.to(device)

                # Forward pass
                outputs = model(inputs)
                _, preds = torch.max(outputs, 1)
                loss = criterion(outputs, labels)

                # Update the running loss and accuracy
                running_loss += loss.item() * inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)

        # Calculate the validation loss and accuracy
        val_loss = running_loss / len(val_dataset)
        val_acc = running_corrects.double() / len(val_dataset)

        # Print the epoch results
        print('Epoch [{}/{}], train loss: {:.4f}, train acc: {:.4f}, val loss: {:.4f}, val acc: {:.4f}'
              .format(epoch+1, num_epochs, train_loss, train_acc, val_loss, val_acc))
```

## Step 7: Fine-tune the model on the custom dataset

Let's now fine-tune the pre-trained ResNet-18 model on the custom dataset by training the last layer for a few epochs and then unfreezing all the layers and training the entire network for a few more epochs.

```python
# Set the device
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
model.to(device)

# Fine-tune the last layer for a few epochs
optimizer = torch.optim.SGD(model.fc.parameters(), lr=0.01, momentum=0.9)
train(model, train_loader, val_loader, criterion, optimizer, num_epochs=5)

# Unfreeze all the layers and fine-tune the entire network for a few more epochs
for param in model.parameters():
    param.requires_grad = True
optimizer = torch.optim.SGD(model.parameters(), lr=0.001, momentum=0.9)
train(model, train_loader, val_loader, criterion, optimizer, num_epochs=10)
```

## Summary

In this tutorial, I described how to fine-tune a pre-trained ResNet-18 model for a domain-specific image classification task using PyTorch. I loaded the pre-trained model, froze the pre-trained layers, modified the last layer, loaded the custom dataset, defined the loss function and optimizer, and trained the fine-tuned model for a specified number of epochs. I also fine-tuned the last layer for a few epochs and then unfroze all the layers and fine-tuned the entire network for a few more epochs.

Fine-tuning pre-trained models is a powerful technique that can significantly reduce the time and computational resources required for training deep neural networks for specific tasks. It is also a common approach in production environments where the datasets are usually small and the training time is limited.

The code snippets provided in this tutorial can serve as a starting point for fine-tuning pre-trained models on domain-specific image classification tasks. However, the approach may need to be adapted to different datasets and tasks, and hyperparameters may need to be tuned for optimal performance.
