# Class 4: How to Design the Data Structure

### **Introduction: What is JSON?**

Before we dive into designing our data structure, let's first understand **what JSON is** and why it is useful.

### **What is JSON?**

**JSON (JavaScript Object Notation)** is a lightweight data format used for storing and exchanging structured data. It is widely used in web applications and APIs due to its simplicity and readability. JSON is easy for both humans and machines to parse and generate.

JSON data is represented as key-value pairs inside **curly brackets `{}`**. Here is a simple example of a JSON object that represents a single movie:

```json
{
  "movie_id": 1,
  "title": "Inception",
  "genre": ["Sci-Fi", "Action"],
  "release_year": 2010,
  "rating": 8.8
}

```

Each movie is stored as an object, and multiple movies can be stored in an array. JSON can be thought of as a structured way to organize data, much like a table or a dictionary in Python.

**Why JSON?**

- **Human-readable:** JSON is simple and easy to understand.
- **Lightweight:** It uses minimal formatting, making it efficient for data exchange.
- **Cross-platform compatibility:** JSON is supported by almost all modern programming languages.
- **Flexible structure:** JSON allows storing complex data like lists, nested objects, and key-value pairs.

Although we will not be implementing JSON in this step, it is crucial to understand its format because we will use it in the next class when we store and retrieve movie data.

---

### **1. What Data Do We Need?**

Before we build the recommendation logic, we need to determine what kind of information is essential for our system to generate recommendations. For a simple movie recommender, we should consider storing the following data:

### **Basic Movie Information**

- **Movie ID**: A unique identifier for each movie (e.g., `1`, `2`, `3`).
- **Title**: The name of the movie (e.g., "Inception").
- **Genre(s)**: The category of the movie (e.g., "Sci-Fi", "Action").
- **Release Year**: The year the movie was released.
- **Rating**: A numerical value representing how well the movie is received (e.g., IMDb rating out of 10).

### **User Preferences (For Future Use)**

- **User ID**: A unique identifier for each user.
- **Watched Movies**: A list of movies the user has already seen.
- **Liked Genres**: The genres that the user prefers.
- **Ratings Given**: The ratings a user has assigned to watched movies.

We will use a simple table to represent this data before deciding on a specific storage format.

---

### **2. Designing a Data Model**

To visualize how our data is structured, let’s define the fields that we need for movies and users:

### **Movies Table (Conceptual Model)**

| **Movie ID** | **Title** | **Genre(s)** | **Release Year** | **Rating** |
| --- | --- | --- | --- | --- |
| 1 | Inception | Sci-Fi, Action | 2010 | 8.8 |
| 2 | The Matrix | Sci-Fi, Action | 1999 | 8.7 |
| 3 | Titanic | Romance, Drama | 1997 | 7.8 |

### **Users Table (Conceptual Model)**

| **User ID** | **Watched Movies** | **Liked Genres** | **Ratings Given** |
| --- | --- | --- | --- |
| 101 | [1, 2] | ["Sci-Fi"] | {1: 9, 2: 8.5} |
| 102 | [3] | ["Romance"] | {3: 8} |

These tables give us a structured way to think about our data before implementing it in a specific format.

---

### **3. How to Convert This Data into JSON?**

Now that we have structured our data conceptually using tables, let’s see how we can represent the same data in JSON format.

For movies:

```json
[
  {
    "movie_id": 1,
    "title": "Inception",
    "genre": ["Sci-Fi", "Action"],
    "release_year": 2010,
    "rating": 8.8
  },
  {
    "movie_id": 2,
    "title": "The Matrix",
    "genre": ["Sci-Fi", "Action"],
    "release_year": 1999,
    "rating": 8.7
  }
]

```

For users:

```json
[
  {
    "user_id": 101,
    "watched_movies": [1, 2],
    "liked_genres": ["Sci-Fi"],
    "ratings_given": {"1": 9, "2": 8.5}
  },
  {
    "user_id": 102,
    "watched_movies": [3],
    "liked_genres": ["Romance"],
    "ratings_given": {"3": 8}
  }
]

```

This conversion ensures that our structured data is stored in a format that can be easily processed by our system.

---

### **4. How the System Will Use This Data**

Now that we have defined our data structure, let’s briefly discuss how the system will utilize this information:

1. **User Input**: The user selects genres they like or rates a few movies.
2. **Filtering**: The system finds movies that match the user’s preferred genres or have high ratings.
3. **Sorting & Ranking**: The system ranks movies based on ratings and similarity to the user’s preferences.
4. **Output**: The recommended movies are displayed to the user.

This logical flow ensures that we can retrieve and process data efficiently.

---

### **5. What’s Next?**

Now that we have outlined the data structure and seen how to convert it into JSON, the next step is to decide how we will store and retrieve it. In **Class 5: How to Get the Data**, we will write Python code to store and manage this information efficiently using JSON.

See you in the next class!