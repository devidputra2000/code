# Soft Computing and Neural Network Programs

## 1. Generate AND + NOT Function Using McCulloch–Pitts Neural Network

```python
num_ip = int(input("ENTER NUMBER OF INPUTS="))
x1, x2 = [], []

for j in range(num_ip):
    x1.append(int(input("X1 = ")))
    x2.append(int(input("X2 = ")))

print("X1 = ", x1)
print("X2 = ", x2)

# AND
w1, w2 = 1, 1
print("AND = ", [x1[i] * w1 + x2[i] * w2 for i in range(num_ip)])

# AND-NOT
w1, w2 = 1, -1
print("AND,NOT = ", [x1[i] * w1 + x2[i] * w2 for i in range(num_ip)])

# NOT
w1 = -1
print("NOT = ", [x1[i] * w1 for i in range(num_ip)])
```

## 2. Generate XOR Function Using a McCulloch–Pitts Neural Network

```python
from sklearn.neural_network import MLPClassifier
import numpy as np

X = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
y = np.array([0, 1, 1, 0])

clf = MLPClassifier(
    hidden_layer_sizes=(2,),
    solver="lbfgs",
    activation="logistic",
    random_state=4
)

clf.fit(X, y)
print("CORRECTED XOR FUNCTION IS=", clf.predict(X))
```

## 3. Implement Hebb's Rule

```python
import numpy as np

x1 = x2 = np.array([1, 1, 1, -1, 1, -1, 1, 1, 1])
y = [1, -1]

w = np.zeros(9, dtype=int)
b = 0

print("\nOUTPUT WITH FIRST TARGET = 1")
w += x1 * y[0]
b += y[0]

print("\nOUTPUT WITH SECOND TARGET = -1")
w += x2 * y[1]
b += y[1]

print("NEW WEIGHT=", w)
print("NEW BIAS=", b)
```

## 4. Implement the Error Backpropagation Algorithm

```python
import math

x, t = 1, 1

c = int(input("Enter Learning Coefficient = "))
w1 = float(input("ENTER W1="))
w2 = float(input("ENTER W2="))
b1 = float(input("ENTER B1="))
b2 = float(input("ENTER B2="))

# Forward Propagation
a1 = math.tanh(w1 * x + b1)
a2 = math.tanh(w2 * a1 + b2)

# Backpropagation
s2 = -2 * (1 - a2**2) * (t - a2)
s1 = (1 - a1**2) * w2 * s2

# Gradient Descent
w1 = w1 - c * s1 * x
w2 = w2 - c * s2 * a1
b1 = b1 - c * s1
b2 = b2 - c * s2

print("NEW W1 = ", w1)
print("NEW W2 = ", w2)
print("NEW B1 = ", b1)
print("NEW B2 = ", b2)
```

## 5. Implement a Hopfield Network

```python
import numpy as np

patterns = np.array([[1, 1, -1, -1], [-1, -1, 1, 1]])

W = sum(np.outer(p, p) for p in patterns)
np.fill_diagonal(W, 0)

test = np.array([1, 1, -1, 1])
output = np.sign(W @ test)
output[output == 0] = 1

print("RETRIEVED/CORRECTED OUTPUT IS = ", output)
```

## 6. Implement a Kohonen Self-Organizing Map (SOM)

```python
# Install first: pip install minisom

import numpy as np
import matplotlib.pyplot as plt
from minisom import MiniSom

colors = np.array([
    [0, 0, 0],
    [0, 0, 1],
    [0, 1, 0],
    [0, 1, 1],
    [1, 0, 0],
    [1, 0, 1],
    [1, 1, 0],
])

color_name = np.array([
    "red", "blue", "darkblue", "skyblue",
    "white", "black", "yellow"
])

som = MiniSom(20, 30, 3, sigma=1.0, learning_rate=0.5)
som.train_random(colors, 400)

plt.imshow(som.get_weights())
plt.title("COLOR KOHONEN SOM")
plt.show()
```

## 7. Demonstrate Linear Separation

```python
import matplotlib.pyplot as plt
import numpy as np

# Define two points to separate
p1, p2 = (1, 4), (3.5, 1.8)

# Set plot axes and labels
plt.xlim(-1, 6)
plt.ylim(-1, 8)
plt.xlabel("Sweetness")
plt.ylabel("Sourness")

plt.plot(p1[0], p1[1], "ro", markersize=10, label="Class A")
plt.plot(p2[0], p2[1], "bo", markersize=10, label="Class B")

# Test different lines to find a separating boundary
X = np.linspace(-0.5, 5, 100)

for angle in np.linspace(0.2, 1.5, 15):
    slope = np.tan(angle)

    # Check whether points lie on opposite sides of y = slope * x
    side1 = (p1[1] - slope * p1[0]) > 0
    side2 = (p2[1] - slope * p2[0]) > 0

    # Green if separated; red otherwise
    color = "g-" if side1 != side2 else "r-"
    plt.plot(X, slope * X, color, alpha=0.6)

plt.title("Linear Separation Demonstration")
plt.legend()
plt.show()
```

## 8. Use Fuzzy Logic to Find Ratios

```python
# Install first: pip install fuzzywuzzy

from fuzzywuzzy import fuzz, process

s1 = "I love fuzzysforfuzzys"
s2 = "I am loving fuzzysforfuzzys"

print("Fuzzywuzzy Ratio:", fuzz.ratio(s1, s2))
print("Fuzzywuzzy Partial Ratio:", fuzz.partial_ratio(s1, s2))
print("Fuzzywuzzy TokenSort Ratio:", fuzz.token_sort_ratio(s1, s2))
print("Fuzzywuzzy TokenSet Ratio:", fuzz.token_set_ratio(s1, s2))
print("Fuzzywuzzy W Ratio:", fuzz.WRatio(s1, s2), "\n")

query = "fuzzy for fuzzys"
choices = ["fuzzy for fuzzys", "fuzzy fuzzy", "g.for fuzzys"]

print("List of ratios:")
print(process.extract(query, choices), "\n")
print("Best among the above list:", process.extractOne(query, choices))
```
