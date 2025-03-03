# Class 6: How to Write the Streamlit Website to Show the Data
Course Link: [https://freaxruby.notion.site/Nano-Movie-Recommender-System-Freax-Ruby-1829520a6b49805e8c4be6a4c862d6e9?pvs=4](https://www.notion.so/Nano-Movie-Recommender-System-Freax-Ruby-1829520a6b49805e8c4be6a4c862d6e9?pvs=21)
**Introduction**

Welcome to Class 6! Now that we have structured our movie data in JSON format and know how to process it in Python, it's time to **create a visually appealing and interactive movie browsing experience** using **Streamlit**.

Unlike previous basic interfaces, we will design an **e-commerce style layout** where:

- Movies are displayed as **individual cards** in a **scrollable list**.
- Users can **filter movies by genre** and **sort by rating or year**.
- A **top-rated movies ranking section** appears on the **right side**, similar to a "Best Sellers" panel.

By the end of this lesson, you will have a **real-world interactive movie browsing app** that looks and feels like a **modern shopping website**.

---

## **1. Setting Up the Streamlit App**

### **Step 1: Import Required Modules**

Create a new Python script named `app.py` and start with the following code:

```python
import streamlit as st
import json
import pandas as pd

```

This imports:

- **Streamlit** (`st`) for building the web UI.
- **JSON** (`json`) for handling movie data.
- **Pandas** (`pd`) for structured data operations.

---

## **2. Loading and Displaying Movies in a User-Friendly Layout**

### **Step 2: Load Movie Data**

```python
def load_movies():
    with open("movies.json", "r") as file:
        return json.load(file)

movies = load_movies()

```

This function reads `movies.json` and stores the data as a list of dictionaries.

---

### **Step 3: Define UI Layout and Style**

```python
st.set_page_config(page_title="Movie Recommender", layout="wide")

st.markdown("""
    <style>
    .big-font { font-size:40px !important; text-align: center; }
    .movie-card {
        border: 1px solid #ddd;
        padding: 15px;
        border-radius: 10px;
        background-color: #f9f9f9;
        margin-bottom: 10px;
    }
    .movie-title {
        font-size: 20px;
        font-weight: bold;
    }
    .movie-details {
        font-size: 16px;
        color: #555;
    }
    </style>
    """, unsafe_allow_html=True)

st.markdown('<p class="big-font">🎬 Movie Recommender System</p>', unsafe_allow_html=True)
st.write("Find movies that match your preferences! 🎥")

```

- Sets the **page title** and **wide layout** for a better display.
- Uses **custom CSS** to create **clean, modern movie cards** with rounded corners.

---

## **3. Implementing Filters and Sorting Options**

### **Step 4: Add Sidebar Filters**

```python
st.sidebar.header("🎭 Filter Options")
genres = list(set(genre for movie in movies for genre in movie["genre"]))
selected_genre = st.sidebar.selectbox("Choose a genre", ["All"] + genres)
sort_option = st.sidebar.selectbox("Sort by", ["Rating (High to Low)", "Rating (Low to High)", "Year (Newest First)", "Year (Oldest First)"])

```

- **Genre filter**: Lets users pick a specific genre or view all movies.
- **Sorting filter**: Allows users to **sort movies dynamically** based on rating or year.

---

### **Step 5: Filter and Sort Movies Based on User Selection**

```python
def filter_and_sort_movies(movies, genre, sort_option):
    filtered = movies if genre == "All" else [movie for movie in movies if genre in movie["genre"]]
    if sort_option == "Rating (High to Low)":
        return sorted(filtered, key=lambda x: x["rating"], reverse=True)
    elif sort_option == "Rating (Low to High)":
        return sorted(filtered, key=lambda x: x["rating"])
    elif sort_option == "Year (Newest First)":
        return sorted(filtered, key=lambda x: x["release_year"], reverse=True)
    elif sort_option == "Year (Oldest First)":
        return sorted(filtered, key=lambda x: x["release_year"])
    return filtered

filtered_movies = filter_and_sort_movies(movies, selected_genre, sort_option)

```

- Filters movies by **selected genre**.
- Sorts them by **rating or year**.

---

## **4. Displaying Movies with a Shopping-Style UI**

### **Step 6: Arrange Layout with Two Columns**

```python
left_col, right_col = st.columns([3, 1])

```

- The **left column (75%)** displays the main movie list.
- The **right column (25%)** holds the **top-rated movies ranking**.

---

### **Step 7: Show Filtered Movies as Cards**

```python
with left_col:
    st.subheader(f"🎬 Movies in {selected_genre} Genre")
    for movie in filtered_movies:
        st.markdown(f"""
        <div class="movie-card">
        <p class="movie-title">{movie['title']}</p>
        <p class="movie-details"><b>Year:</b> {movie['release_year']}<br>
        <b>Genre:</b> {', '.join(movie['genre'])}<br>
        <b>Rating:</b> {'⭐' * int(movie['rating']//2)} ({movie['rating']})</p>
        </div>
        """, unsafe_allow_html=True)

```

Each movie is displayed **inside a neatly formatted card**, similar to an **e-commerce product listing**.

---

## **5. Adding a "Best Movies" Ranking on the Right**

### **Step 8: Display Top-Rated Movies**

```python
with right_col:
    st.subheader("🏆 Top 5 Rated Movies")
    top_movies = sorted(movies, key=lambda x: x["rating"], reverse=True)[:5]
    for idx, movie in enumerate(top_movies):
        st.markdown(f"""
        <div class="movie-card">
        <p class="movie-title">{idx+1}. {movie['title']}</p>
        <p class="movie-details"><b>Rating:</b> {'⭐' * int(movie['rating']//2)} ({movie['rating']})</p>
        </div>
        """, unsafe_allow_html=True)

```

- **Right-side panel** always shows **top 5 highest-rated movies**.
- Uses **ranking numbers** (1, 2, 3...) and **stars** for better readability.

---

## **6. Running the Streamlit App**

### **Step 9: Launch the Web App**

```bash
streamlit run app.py

```

This command will:

- Open the **interactive movie recommender** in a browser.
- Let users **filter, sort, and browse** movies in **real-time**.

---

## **7. Summary and Next Steps**

### **What We Built**

✅ A **modern UI** with a **shopping-style** layout.

✅ A **sidebar filter** to select genres and sorting options.

✅ A **scrollable movie list** that updates dynamically.

✅ A **top-rated movies ranking panel** on the right.

### **What’s Next? (Class 7)**

- **Personalized movie recommendations** based on user history.
- **Handling new users (cold start problem).**
- **Expanding beyond simple filtering to AI-powered recommendations.**

See you in Class 7! 🚀

# Appendix: JSON Data and Python Script

## `app.py`

```python
import streamlit as st
import json
import pandas as pd

# Load movie data
def load_movies():
    with open("movies.json", "r") as file:
        return json.load(file)

movies = load_movies()

st.set_page_config(page_title="Movie Recommender", layout="wide")

st.markdown("""
    <style>
    .big-font { font-size:40px !important; text-align: center; }
    .movie-card {
        border: 1px solid #ddd;
        padding: 15px;
        border-radius: 10px;
        background-color: #f9f9f9;
        margin-bottom: 10px;
    }
    .movie-title {
        font-size: 20px;
        font-weight: bold;
    }
    .movie-details {
        font-size: 16px;
        color: #555;
    }
    </style>
    """, unsafe_allow_html=True)

st.markdown('<p class="big-font">🎬 Movie Recommender System</p>', unsafe_allow_html=True)
st.write("Find movies that match your preferences! 🎥")

st.sidebar.header("🎭 Filter Options")
genres = list(set(genre for movie in movies for genre in movie["genre"]))
selected_genre = st.sidebar.selectbox("Choose a genre", ["All"] + genres)
sort_option = st.sidebar.selectbox("Sort by", ["Rating (High to Low)", "Rating (Low to High)", "Year (Newest First)", "Year (Oldest First)"])

def filter_and_sort_movies(movies, genre, sort_option):
    filtered = movies if genre == "All" else [movie for movie in movies if genre in movie["genre"]]
    if sort_option == "Rating (High to Low)":
        return sorted(filtered, key=lambda x: x["rating"], reverse=True)
    elif sort_option == "Rating (Low to High)":
        return sorted(filtered, key=lambda x: x["rating"])
    elif sort_option == "Year (Newest First)":
        return sorted(filtered, key=lambda x: x["release_year"], reverse=True)
    elif sort_option == "Year (Oldest First)":
        return sorted(filtered, key=lambda x: x["release_year"])
    return filtered

filtered_movies = filter_and_sort_movies(movies, selected_genre, sort_option)

left_col, right_col = st.columns([3, 1])

with left_col:
    st.subheader(f"🎬 Movies in {selected_genre} Genre")
    for movie in filtered_movies:
        st.markdown(f"""
        <div class="movie-card">
        <p class="movie-title">{movie['title']}</p>
        <p class="movie-details"><b>Year:</b> {movie['release_year']}<br>
        <b>Genre:</b> {', '.join(movie['genre'])}<br>
        <b>Rating:</b> {'⭐' * int(movie['rating']//2)} ({movie['rating']})</p>
        </div>
        """, unsafe_allow_html=True)

with right_col:
    st.subheader("🏆 Top 5 Rated Movies")
    top_movies = sorted(movies, key=lambda x: x["rating"], reverse=True)[:5]
    for idx, movie in enumerate(top_movies):
        st.markdown(f"""
        <div class="movie-card">
        <p class="movie-title">{idx+1}. {movie['title']}</p>
        <p class="movie-details"><b>Rating:</b> {'⭐' * int(movie['rating']//2)} ({movie['rating']})</p>
        </div>
        """, unsafe_allow_html=True)

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