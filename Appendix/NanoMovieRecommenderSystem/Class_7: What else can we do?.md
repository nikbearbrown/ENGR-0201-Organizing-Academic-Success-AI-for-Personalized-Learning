# Class 7: What Else Can We Do?
Course Link: [https://freaxruby.notion.site/Nano-Movie-Recommender-System-Freax-Ruby-1829520a6b49805e8c4be6a4c862d6e9?pvs=4](https://www.notion.so/Nano-Movie-Recommender-System-Freax-Ruby-1829520a6b49805e8c4be6a4c862d6e9?pvs=21)
### **Introduction**

Welcome to **Class 7**! In this class, we will focus on how **user activity**—such as watch history, ratings, and interactions with the system—can impact and enhance the effectiveness of **recommendation systems**. We’ll dive into the **cold start problem**, how **new ratings** influence the recommendations, and how user interactions, like **browsing behavior** and **time-based preferences**, shape the system’s dynamic adjustments.

By the end of this class, you'll be able to:

1. Address the **cold start problem** effectively with smart solutions.
2. Understand how **new ratings** influence the evolution of recommendations.
3. Learn how **user activity** and interaction with the system can be used to adjust suggestions in real-time.
4. Use **time-sensitive patterns** to make more **personalized** recommendations based on when a user interacts with the system.

---

### **1. The Cold Start Problem**

### **What is the Cold Start Problem?**

The **cold start problem** arises when a recommendation system has insufficient data about new users or new items. This makes it difficult to provide **personalized recommendations**.

- **New User Cold Start**: When a new user joins the platform, the system has no historical data to base recommendations on, making it hard to predict their preferences.
- **New Item Cold Start**: When a new movie is added, no one has rated it yet, making it difficult to determine who might like it.

### **Solutions for Cold Start Problems:**

### **For New Users:**

- **Onboarding Questionnaires**: Ask new users to select their favorite genres or types of movies when they first sign up. This helps the system create an initial profile to start recommending.
- **Popularity-Based Recommendations**: Until personalized preferences are gathered, recommend popular or top-rated movies.
- **Demographic Data**: If available, use basic demographic information (such as age, gender, or location) to make initial guesses about preferences.

### **For New Items:**

- **Content-Based Filtering**: Recommend movies based on shared attributes like genre, director, or actor. For example, if a user likes action movies, the system can recommend other action movies, even if they are new.
- **External Reviews**: Use external reviews (e.g., IMDb or Rotten Tomatoes) to assign an initial score to new movies and recommend them based on similarity to movies the user has already watched.

### **Practical Example of Cold Start**:

If a user selects a genre preference such as **Drama**, the system can recommend popular **drama movies** that the user has not seen yet.

```python
selected_genres = st.multiselect("Select your favorite genres:", genres)
if selected_genres:
    recommended_movies = [movie for movie in movies if any(genre in movie["genre"] for genre in selected_genres)]
st.write(recommended_movies)

```

---

### **2. The Impact of New Ratings on Recommendations**

As users rate movies, the system gains more **insights** into their preferences. These ratings help the system **refine its recommendations** to better align with what the user actually enjoys.

### **How New Ratings Affect Recommendations**:

When a user rates a movie, the system gets **explicit feedback** that helps refine recommendations. For instance, if a user rates several **action movies** highly, the system will prioritize action movies in future recommendations.

### **Simulation Example:**

Imagine Alice has rated the following movies:

- **The Dark Knight** (9/10) - Action, Crime
- **Avengers: Endgame** (8.4/10) - Action, Sci-Fi

Before Alice adds any ratings, the system might recommend more **action movies** based on her watch history. Once Alice adds ratings to these movies, the system will update her profile, and future recommendations will become more **refined**.

- **Before Rating**: The system recommends movies that fit Alice’s general **genre** preferences.
- **After Rating**: The system prioritizes movies from the **Action** genre, as Alice has explicitly rated action films highly.

### **Updated Recommendations**:

If Alice adds new ratings, the system will update her genre preferences and display new suggestions accordingly. For example, after adding the movie **Parasite** (rated 8.6), the system will recognize a shift in genre preferences (Action to include Drama) and suggest movies that align with these updated preferences.

```python
new_movie = 'Parasite'
new_rating = 8.6  # User rated Parasite as 8.6 stars

# Update user's genre preferences based on the new rating
user_ratings[new_movie] = new_rating
updated_recommendations = update_recommendations(user_ratings)

```

---

### **3. How User Activity Impacts Recommendations**

A recommendation system learns continuously from **user activity**. This includes not just ratings but also **watch history** and **browsing behavior**. The system adjusts recommendations based on these interactions, making the system more **dynamic** and responsive to the user’s changing preferences.

### **Types of User Activity**:

1. **Watch History**:
    - The system tracks which movies the user has watched to detect **preferences** in genres, actors, or directors. Recently watched movies often weigh more heavily in recommendations.
2. **Explicit Ratings**:
    - When a user rates a movie (e.g., from 1-5 stars), the system can **re-prioritize** recommendations based on the user’s tastes, especially for genres or types of movies they rate highly.
3. **Browsing Behavior**:
    - Even if a user doesn’t explicitly rate a movie, the system can track **what they click on**, how long they spend looking at a movie, or whether they add a movie to their watchlist. These implicit signals are valuable in refining recommendations.
4. **Time-Based Preferences**:
    - Users may have different preferences based on the **time of day** or **season**. For instance, users might prefer more **action-packed movies** on weekends and **documentaries** on weekdays. Recognizing these patterns allows the system to recommend movies based on **time-sensitive contexts**.

---

### **4. Time-Based Preferences in Recommendations**

Time-sensitive recommendations are based on **when** a user interacts with the system. For example, a user might prefer **action movies** during weekends and more **relaxing genres** like **documentaries** during weekdays. Accounting for **time-based preferences** can improve the relevance of recommendations.

### **Time-Sensitive Recommendations**:

- **Weekend Evening**: Users might enjoy **action** or **comedy** movies to unwind after a busy week.
- **Weekday Morning**: Users might prefer **documentaries** or shorter films in the morning.

### **Practical Example: Time-Based Adjustments**:

If the system detects the user is browsing in the **evening**, it can recommend **action** movies. Conversely, during **weekday mornings**, the system might prioritize more **light** content such as **dramas** or **documentaries**.

```python
# Time-based logic to recommend different movies based on the time of day
time_of_day = get_current_time_of_day()

if time_of_day == "evening":
    recommended_movies = recommend_action_or_comedy()
elif time_of_day == "morning":
    recommended_movies = recommend_documentaries()

```

---

### **5. Detailed Example: Simulation of New Watch History and Ratings**

In the example below, we simulate a user named **Alice** adding new movies to their watch history and updating their ratings:

### **Updated Watch History**:

- **Before Genre Preferences**: Alice had a strong preference for **Sci-Fi** and **Action** movies.
- **After Adding Movies**: Alice watched **Parasite** (rated 8.6) and added it to her watch history. As a result, her genre preferences now include **Drama** in addition to **Sci-Fi** and **Action**.

The system then updates its recommendations, now suggesting movies that match Alice’s **updated preferences**.

### **Example of Updated Recommendations**:

```
Updated Recommendations:
- **The Godfather** (Drama, Crime) - Rated 9.2/10. Recommended because it matches your updated preference for Drama.
- **Forrest Gump** (Drama, Romance) - Rated 8.8/10. Recommended because it matches your updated preference for Drama.
- **Avengers: Endgame** (Action, Sci-Fi) - Rated 8.4/10. Recommended because it matches your updated preference for Sci-Fi.

```

---

### **Key Takeaways**:

1. **Cold Start Problem**: Address the cold start problem by using **questionnaires** and **content-based filtering** to recommend movies when there is insufficient user or item data.
2. **Impact of Ratings**: Ratings provide explicit feedback that helps the system better understand a user’s preferences and refine future recommendations.
3. **User Activity**: Tracking **watch history**, **ratings**, and **browsing behavior** allows the system to dynamically update its recommendations based on **user activity**.
4. **Time-Based Preferences**: Adjusting recommendations based on when users interact with the system can provide more **context-aware**, **personalized** suggestions.

### **Next Steps**:

1. **Experiment with Hybrid Approaches**: Combine **content-based** and **collaborative filtering** to enhance recommendation accuracy.
2. **Refine Time-Based Logic**: Implement more nuanced time-based recommendations by analyzing user interaction patterns throughout the day or season.
3. **Deploy the System**: Use cloud platforms like **Streamlit Cloud** or **Heroku** to deploy your recommender system and observe its real-world performance.

With these steps, you can continue building a smarter, more personalized recommendation system that dynamically adapts to users' evolving preferences.

**Happy coding!**