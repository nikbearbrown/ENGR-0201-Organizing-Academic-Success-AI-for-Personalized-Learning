# Class 3: How to Set Up the Development Environment(part 1)

# **Introduction**

Hello everyone, and welcome to the third class! In the previous lesson, we designed the structure of our Nano Recommender System. Now, before we start coding, we need to set up our development environment properly.

Today, we will learn how to Install and configure **Conda** for managing our Python environment.

Let's get started!

---

# **Installing Conda**

### **What is Conda? — A Kitchen Analogy (Even for Non-Programmers!)**

**Conda is like a smart kitchen management system that keeps every recipe organized, ensuring the right ingredients and tools are used without mix-ups.**

---

### **Why Do We Need Conda?**

Imagine you are a chef running a restaurant. You need to prepare different dishes:

🍜 **Ramen** needs **a pot + noodles + broth**

🍰 **Cake** needs **an oven + flour + milk**

Now, what if you manage everything in the same kitchen without separation?

- You try to bake a cake in a pot instead of an oven? ❌ (Wrong tool for the job)
- You accidentally use flour instead of noodles for ramen? ❌ (Ingredients are not compatible)
- You upgrade your flour brand, and suddenly the cake improves, but the ramen sauce thickens too much because it also used flour in a different way! ❌ (A single change affects multiple recipes unexpectedly)

**This is exactly what happens when you install different software tools (like Python and machine learning libraries) without Conda!**

### **How Conda Fixes This**

Conda lets you create **separate kitchens** for each dish, so you never mix up tools or ingredients.

1️⃣ **Create a kitchen for ramen**

```bash
conda create -n ramen_kitchen cooking_tool=pot noodles=1.0 broth=1.0

```

2️⃣ **Create a kitchen for cake**

```bash
conda create -n cake_kitchen cooking_tool=oven flour=2.0 milk=1.5

```

Now, when you want to cook ramen, you **step into the ramen kitchen**:

```bash

conda activate ramen_kitchen
```

And when you want to bake a cake, you **step into the cake kitchen**:

```bash

conda activate cake_kitchen
```

This means:

✅ The ramen kitchen always has the right pot, noodles, and broth.

✅ The cake kitchen always has the right oven, flour, and milk.

✅ If you upgrade flour for the cake, it won’t accidentally ruin the ramen!

# **Now, What About Python?**

Now that we understand the importance of keeping different kitchens for different dishes, let’s see how this idea applies to **Python and programming**.

---

### **1. Python is Like a Kitchen Appliance**

Just like a kitchen needs the right **appliance** to cook different dishes, a program needs the right **Python version** to run properly.

- A **pot** is perfect for boiling ramen → **Python 3.8** is good for certain projects.
- An **oven** is better for baking a cake → **Python 3.10** is required for newer projects.

If you try to bake a cake in a pot or cook ramen in an oven, it won’t work well. Similarly, if a program needs **Python 3.8** but you run it with **Python 3.10**, it might break.

---

### **2. Python Libraries Are Like Ingredients**

Each dish requires specific ingredients, just like Python programs require **libraries** (pre-made tools that help with tasks).

- **Ramen** needs **broth** → **NumPy** (used for numerical calculations).
- **Ramen** needs **noodles** → **TensorFlow** (used for machine learning).
- **Cake** needs **flour** → **Pandas** (used for data processing).
- **Cake** needs **milk** → **Matplotlib** (used for making charts).

If you use the wrong ingredients, your dish won’t turn out well. Similarly, if a Python project requires a specific version of a library and you update it, it might stop working.

---

### **3. The Problem Without Conda**

If you don’t organize your kitchen, you’ll run into problems:

- You **upgrade flour (Pandas 1.3) for cake**, but now **your ramen broth (NumPy 1.19) doesn’t thicken properly**.
- You **replace the pot (Python 3.8) with an oven (Python 3.10) to bake cakes**, but now **ramen takes forever to cook**.
- You **buy a new type of milk (Matplotlib 3.5) for the cake**, but now **it changes the texture of the flour (Pandas 1.3)**.

This is called **a dependency conflict**—some projects need older versions of Python or libraries, while others need newer ones.

---

### **4. How Conda Fixes This**

Conda lets you create **separate cooking stations** (Python environments) so every recipe works perfectly:

🍜 **Ramen kitchen**: Uses a **pot (Python 3.8) + proper broth (NumPy 1.19) + proper noodles (TensorFlow 2.4)**

```bash

conda create -n ramen_kitchen python=3.8 numpy=1.19 tensorflow=2.
```

🍰 **Cake kitchen**: Uses an **oven (Python 3.10) + proper flour (Pandas 1.3) + proper milk (Matplotlib 3.5)**

```bash

conda create -n cake_kitchen python=3.10 pandas=1.3 matplotlib=3.5
```

Now, when you want to work on a project, you **step into the right kitchen**:

```bash

conda activate ramen_kitchen
```

or

```bash

conda activate cake_kitchen
```

This ensures:

✅ **Each project runs the correct Python version (appliance).**

✅ **Each project uses the correct libraries (ingredients).**

✅ **Changes in one project do not break another.**

# **Step-by-Step Guide to Installing Conda (For Absolute Beginners)**

Conda is included in **Anaconda** and **Miniconda**.

- **Anaconda**: Comes with Conda and many pre-installed libraries (good for beginners).
- **Miniconda**: A smaller version that only includes Conda (good for advanced users).

If you are new to Python, I recommend installing **Anaconda**.

---

### **Step 1: Download Anaconda**

1. Go to the official Anaconda website:
👉 https://www.anaconda.com/download
2. Choose the correct version for your operating system:
    - **Windows**
    - **Mac** (Intel or Apple Silicon)
    - **Linux**
3. Click **Download** and wait for the file to finish downloading.

---

### **Step 2: Install Anaconda**

### **Windows**

1. Open the downloaded `.exe` file.
2. Click **Next** to start the installation.
3. Choose **“Just Me”** (recommended for most users).
4. Click **Install** and wait for it to finish.
5. When done, click **Finish**.

### **Mac**

1. Open the downloaded `.pkg` file.
2. Follow the instructions and click **Continue**.
3. Once installed, open **Terminal** (Search "Terminal" on Mac).

### **Linux**

1. Open **Terminal**.
2. Navigate to the download location:
    
    ```bash
    cd ~/Downloads
    
    ```
    
3. Run the installation script:
    
    ```bash
    bash Anaconda3-*.sh
    
    ```
    
4. Follow the instructions and type **yes** when asked.

---

### **Step 3: Verify Installation**

After installation, open **Command Prompt (Windows)** or **Terminal (Mac/Linux)** and type:

```bash
conda --version

```

If Conda is installed correctly, you will see something like:

```
conda 23.1.0
```

---

### **Step 4: Create Your First Conda Environment**

Now that Conda is installed, let’s create an environment to keep things organized.

```bash
conda create -n my_first_env python=3.9
```

- `my_first_env` is the name of your environment (you can change it).
- `python=3.9` installs Python 3.9 in this environment.

When asked **Proceed ([y]/n)?**, type:

```bash
y
```

---

### **Step 5: Activate Your Environment**

To use your new environment, activate it:

```bash
conda activate my_first_env
```

You will see `(my_first_env)` appear before your command line, meaning you are inside that environment.

---

### **Step 6: Install a Package**

To install a Python library (e.g., NumPy), use:

```bash
conda install numpy
```

This will install NumPy in **your environment** without affecting other projects.

---

### **Step 7: Deactivate and Exit**

When you are done, exit the environment with:

```bash
conda deactivate
```

This returns you to the base system.

---

### **Congratulations! 🎉**

You have successfully installed Conda and created your first Python environment! 🚀