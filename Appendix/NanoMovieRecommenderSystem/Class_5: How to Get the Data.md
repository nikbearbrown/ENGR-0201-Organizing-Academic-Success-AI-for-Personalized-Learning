# Class 5: How to Get the Data
Course Link: [https://freaxruby.notion.site/Nano-Movie-Recommender-System-Freax-Ruby-1829520a6b49805e8c4be6a4c862d6e9?pvs=4](https://www.notion.so/Nano-Movie-Recommender-System-Freax-Ruby-1829520a6b49805e8c4be6a4c862d6e9?pvs=21)

## **Introduction**

Welcome back! In the previous class, we designed our **data structure** and learned how to represent it in **JSON format**. Now, it's time to take the next step: **retrieving and processing JSON data** using Python.

This lesson is designed for complete beginners, so don’t worry if you have no prior programming experience. We will go step by step and explain every line in detail.

### **What You Will Learn**

By the end of this lesson, you will be able to:

1. **Load JSON data into Python.**
2. **Access and manipulate JSON data.**
3. **Filter data to retrieve specific information.**
4. **Work with both movie and user data stored in JSON format.**

Let’s get started!

---

## **1. Loading JSON Data in Python**

### **Step 1: Importing Required Libraries**

```python
import json  # Built-in module for working with JSON files

```

**What it does:**

- `import json` tells Python to load the built-in `json` module.
- This module provides functions for reading, writing, and manipulating JSON data.
- JSON (JavaScript Object Notation) is a format used for storing and exchanging structured data.

---

### **Step 2: Opening a JSON File**

```python
with open("movies.json", "r") as file:
    movies = json.load(file)

```

**What it does:**

- `open("movies.json", "r")`: Opens the file named `movies.json` in **read mode** (`"r"` stands for **read**).
- `with open(...) as file`: Uses a **context manager** to ensure the file is automatically closed after it has been read.
- `json.load(file)`: Reads the content of the JSON file and **converts it into a Python data structure** (typically a list of dictionaries).
- `movies = json.load(file)`: Stores the converted data into the `movies` variable.

---

### **Step 3: Verifying Loaded Data**

```python
print(movies)

```

**What it does:**

- Displays the loaded data to verify that it has been read correctly.
- Prints a list of dictionaries, where each dictionary represents a movie.

---

## **2. Accessing and Manipulating JSON Data**

### **Step 4: Looping Through Movies**

```python
for movie in movies:
    print("Movie Title:", movie["title"])

```

**What it does:**

- `for movie in movies`: Iterates over the list `movies`, where each item is a dictionary representing a movie.
- `movie["title"]`: Accesses the `title` field in each dictionary.
- `print(...)`: Prints the title of each movie one by one.

**Example Output:**

```
Movie Title: Inception
Movie Title: The Matrix

```

---

### **Step 5: Extracting Movies by Genre**

```python
def get_movies_by_genre(movies, genre):
    return [movie for movie in movies if genre in movie["genre"]]

```

**What it does:**

- `def get_movies_by_genre(movies, genre)`: Defines a function that takes a **list of movies** and a **genre** as arguments.
- `movie for movie in movies if genre in movie["genre"]`: Uses **list comprehension** to filter out movies that belong to the given genre.
- Returns a **list of movies** that match the genre.

---

### **Step 6: Displaying Filtered Movies**

```python
sci_fi_movies = get_movies_by_genre(movies, "Sci-Fi")
print(sci_fi_movies)

```

**What it does:**

- Calls the function `get_movies_by_genre(movies, "Sci-Fi")` to filter movies that belong to the "Sci-Fi" genre.
- Prints the filtered list of Sci-Fi movies.

---

## **3. Basic Filtering Operations**

### **Step 7: Finding Top-Rated Movies**

```python
sorted_movies = sorted(movies, key=lambda x: x["rating"], reverse=True)
print("Top 3 Rated Movies:", sorted_movies[:3])

```

**What it does:**

- `sorted(movies, key=lambda x: x["rating"], reverse=True)`: Sorts the movies based on their **rating** in descending order.
- `lambda x: x["rating"]`: A **lambda function** (anonymous function) used to extract the rating from each movie dictionary.
- `sorted_movies[:3]`: Retrieves the **top 3 movies** with the highest ratings.

---

### **Step 8: Filtering Movies by Release Year**

```python
def get_movies_by_year(movies, year):
    return [movie for movie in movies if movie["release_year"] >= year]

```

**What it does:**

- Defines a function that filters movies released after a specified year.
- Uses **list comprehension** to return only movies that satisfy the condition.

---

## **4. Loading and Managing User Data**

### **Step 9: Loading User Data**

```python
with open("users.json", "r") as file:
    users = json.load(file)

```

**What it does:** Loads user data from `users.json` using the same method as before.

---

### **Step 10: Finding a User’s Favorite Movies**

```python
def get_user_favorites(user_id):
    for user in users:
        if user["user_id"] == user_id:
            return user["watched_movies"]
    return []

```

**What it does:**

- Defines a function that finds movies watched by a specific user.
- Loops through the list of users to find the matching `user_id`.
- If found, returns the list of `watched_movies`; otherwise, returns an empty list.

```python
user_101_favorites = get_user_favorites(101)
print("User 101's Favorite Movies:", user_101_favorites)

```

**What it does:** Calls the function to retrieve movies watched by user `101` and prints the results.

---

## **5. Running and Testing the Code**

### **Step 11: Running the Python Script**

```bash
python load_data.py

```

**What it does:** Runs the Python script to ensure everything works correctly.

---

## **6. What’s Next?**

Now that we can **load, access, and filter JSON data**, we are ready to build our recommendation system.

### **In Class 6, We Will Learn:**

- How to display JSON data interactively using **Streamlit**.
- How to allow users to explore and filter movies in a web app.
- How to prepare for building a recommendation system.

See you in the next class!

# Appendix: JSON Data and Python Script

## `load_data.py`

```python
import json  # Built-in module for working with JSON files

# Step 2: Opening a JSON File
def load_movies(filename):
    with open(filename, "r") as file:
        return json.load(file)

# Step 3: Verifying Loaded Data
def print_movies(movies):
    print(movies)

# Step 4: Looping Through Movies
def print_movie_titles(movies):
    for movie in movies:
        print("Movie Title:", movie["title"])

# Step 5: Extracting Movies by Genre
def get_movies_by_genre(movies, genre):
    return [movie for movie in movies if genre in movie["genre"]]

# Step 6: Displaying Filtered Movies
def print_sci_fi_movies(movies):
    sci_fi_movies = get_movies_by_genre(movies, "Sci-Fi")
    print(sci_fi_movies)

# Step 7: Finding Top-Rated Movies
def get_top_rated_movies(movies, top_n=3):
    sorted_movies = sorted(movies, key=lambda x: x["rating"], reverse=True)
    return sorted_movies[:top_n]

# Step 8: Filtering Movies by Release Year
def get_movies_by_year(movies, year):
    return [movie for movie in movies if movie["release_year"] >= year]

# Step 9: Loading User Data
def load_users(filename):
    with open(filename, "r") as file:
        return json.load(file)

# Step 10: Finding a User’s Favorite Movies
def get_user_favorites(users, user_id):
    for user in users:
        if user["user_id"] == user_id:
            return user["watched_movies"]
    return []

if __name__ == "__main__":
    # Load movies and users
    movies = load_movies("movies.json")
    users = load_users("users.json")

    # Print loaded movies
    print_movies(movies)

    # Print movie titles
    print_movie_titles(movies)

    # Print Sci-Fi movies
    print_sci_fi_movies(movies)

    # Print top-rated movies
    top_movies = get_top_rated_movies(movies)
    print("Top 3 Rated Movies:", top_movies)

    # Print movies from a specific year
    movies_after_2010 = get_movies_by_year(movies, 2010)
    print("Movies released after 2010:", movies_after_2010)

    # Print user favorite movies
    user_101_favorites = get_user_favorites(users, 101)
    print("User 101's Favorite Movies:", user_101_favorites)

```

## `movies.json`

```json
[
    {
        "title": "Inception",
        "genre": ["Sci-Fi", "Action"],
        "rating": 8.8,
        "release_year": 2010
    },
    {
        "title": "The Matrix",
        "genre": ["Sci-Fi", "Action"],
        "rating": 8.7,
        "release_year": 1999
    },
    {
        "title": "Interstellar",
        "genre": ["Sci-Fi", "Drama"],
        "rating": 8.6,
        "release_year": 2014
    },
    {
        "title": "The Dark Knight",
        "genre": ["Action", "Crime"],
        "rating": 9.0,
        "release_year": 2008
    },
    {
        "title": "Forrest Gump",
        "genre": ["Drama", "Romance"],
        "rating": 8.8,
        "release_year": 1994
    },
    {
        "title": "Parasite",
        "genre": ["Thriller", "Drama"],
        "rating": 8.6,
        "release_year": 2019
    },
    {
        "title": "Avengers: Endgame",
        "genre": ["Action", "Sci-Fi"],
        "rating": 8.4,
        "release_year": 2019
    },
    {
        "title": "The Godfather",
        "genre": ["Crime", "Drama"],
        "rating": 9.2,
        "release_year": 1972
    }
]

```

## `users.json`

```json
[
    {
        "user_id": 101,
        "name": "Alice",
        "watched_movies": ["Inception", "The Matrix", "Interstellar"]
    },
    {
        "user_id": 102,
        "name": "Bob",
        "watched_movies": ["The Dark Knight", "Forrest Gump", "Parasite"]
    },
    {
        "user_id": 103,
        "name": "Charlie",
        "watched_movies": ["Avengers: Endgame", "The Matrix", "The Godfather"]
    },
    {
        "user_id": 104,
        "name": "David",
        "watched_movies": ["Inception", "The Dark Knight", "Parasite"]
    },
    {
        "user_id": 105,
        "name": "Emma",
        "watched_movies": ["Forrest Gump", "Interstellar", "The Godfather"]
    }
]

```