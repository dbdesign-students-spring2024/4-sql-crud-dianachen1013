# SQL CRUD

## Part 1: Restaurant finder

Design a database table named `restaurants` that would allow an application that uses it to find restaurants and a table named `reviews` that would hold reviews for any restaurant.

### Creat tables: [restaurants](data/restaurants.csv) & reviews

- **id** (restaurant number:primary key): An integer ranges from 1 to 1000, each restaurant matches with a unique id number.
- **category** (genre of food: text): Chinese,Italian,Japanese,Korean,Thai,Spanish,Indian, Mexican.
- **name** (restaurant names: text): Witherspoon Grill, ilili, Kimura, Take31, Gelso & Grand, Bonqueria, Fish Cheeks, MayRee, Tartinery, Su Jeo, Essential by Chris, Chipotle, Wendy's, Five Guys, Don Angie, Bo Ky, Big Wong, Antoya, Hello Saigon, Temple Court.
- **price_tier** (cost level: text): Cheap, Medium, or Expensive
- **neighborhood** (a particular NYC neighborhood: text): Tribeca, Soho, Little Italy, Gramercy, Greenwich Village, West Village, East Village, Upper East Side, Upper West Side,Lower East Side, Flushing, Chelsea, Queens.
- **opening_hour** (open at what time: text): From 9:00 to 12:00 in a 24 hours fomat.
- **closing_hour** (open at what time: text): From 16:00 to 21:00 in a 24 hours fomat.
- **average_rating** (stars: integer): 1, 2, 3, 4, 5.
- **good_for_kids** (good or not: boolean): True, False

The application must also be able to allow users to leave reviews associated with any restaurant.

Write the SQL commands to create the tables with the structure you determine is necessary.
I first create the form "restaurant.csv" with the following sql code.
```sql
CREATE TABLE Restaurants (
    id INT PRIMARY KEY,
    category TEXT,
    name TEXT UNIQUE NOT NULL,
    price_tier TEXT,
    neighborhood TEXT,
    opening_hour TIME,
    closing_hour TIME,
    average_rating INT,
    good_for_kids BOOLEAN
);
```
Then, I create a table named "reviews".
```sql
CREATE TABLE reviews (
    review_id INT PRIMARY KEY,
    restaurant_id INT,
    review_text TEXT,
    rating INT,
    FOREIGN KEY (restaurant_id) REFERENCES Restaurants(id)
);
```
Once I get the forms, I prepared my SQL Database:
```sql
sqlite3 restaurant_data.db
```
To import the restaurants.csv file into my SQLite database, I set the mode to CSV and import the data:
```sql
.mode csv
.import /"C:/Users/86136/Desktop/Database/workshop/4-sql-crud-dianachen1013/data/restaurants.csv"/to/restaurants.csv Restaurants
```
### Queries
For the following questions, I will write a single SQL query to perform each of the folloing tasks, and show the results in separate tables.

1. Find all cheap restaurants in a particular neighborhood (neighborhood: Greenwich Village).
```sql
SELECT *
FROM Restaurants
WHERE price_tier = 'Cheap'
AND neighborhood = 'Greenwich Village';
```
I get 20 results in total, I choose the first 5 rows to show in the following table:

| id  | category | name               | price_tier | neighborhood       | opening hour | closing hour | average_rating | good_for_kids |
|-----|----------|--------------------|------------|--------------------|--------------|--------------|----------------|---------------|
| 2   | Thai     | Witherspoon Grill | Cheap      | Greenwich Village  | 9:00         | 17:00        | 5              | FALSE         |
| 55  | Japanese | Take31            | Cheap      | Greenwich Village  | 11:00        | 18:00        | 5              | TRUE          |
| 93  | Japanese | Tartinery         | Cheap      | Greenwich Village  | 9:00         | 17:00        | 4              | TRUE          |
| 98  | Chinese  | Don Angie         | Cheap      | Greenwich Village  | 10:00        | 16:00        | 1              | TRUE          |
| 132 | Japanese | Chipotle          | Cheap      | Greenwich Village  | 10:00        | 20:00        | 1              | FALSE         |


2. Find all restaurants in a particular genre (genre: Thai) with 3 stars or more, ordered by the number of stars in descending order.
```sql
SELECT *
FROM Restaurants
WHERE category = 'Thai'
AND average_rating >= 3
ORDER BY average_rating DESC;
```
There are 79 results in total, here are the top 5 restaurants:

| id  | category | name              | price_tier | neighborhood       | opening hour | closing hour | average_rating | good_for_kids |
|-----|----------|-------------------|------------|--------------------|--------------|--------------|----------------|---------------|
| 2   | Thai     | Witherspoon Grill | Cheap      | Greenwich Village  | 9:00         | 17:00        | 5              | FALSE         |
| 33  | Thai     | Gelso & Grand     | Expensive  | Soho               | 10:30        | 20:00        | 5              | TRUE          |
| 37  | Thai     | Don Angie         | Expensive  | Upper East Side    | 9:30         | 18:00        | 5              | FALSE         |
| 57  | Thai     | MayRee            | Expensive  | Upper West Side    | 10:00        | 16:00        | 5              | TRUE          |
| 69  | Thai     | Antoya            | Cheap      | Upper West Side    | 10:00        | 17:00        | 5              | FALSE         |

3. Find all restaurants that are open now (see hint below).
My current time is 09:47 in the morning, I'll use the following SQL code to track all the restaurants that are open.
```sql
SELECT *
FROM Restaurants
WHERE strftime('%H:%M', 'now', 'localtime') BETWEEN opening_hour AND closing_hour;
```
By replacing '%H:%M' to 09:47, here are the 344 restaurants that are available at this moment.
| id | category | name               | price_tier | neighborhood       | opening hour | closing hour | average_rating | good_for_kids |
|----|----------|--------------------|------------|--------------------|--------------|--------------|----------------|---------------|
| 2  | Thai     | Witherspoon Grill  | Cheap      | Greenwich Village  | 9:00         | 17:00        | 5              | FALSE         |
| 5  | Chinese  | ilili              | Expensive  | Lower East Side    | 9:00         | 16:00        | 4              | FALSE         |
| 10 | Indian   | Gelso & Grand      | Cheap      | Chelsea            | 9:00         | 21:00        | 3              | FALSE         |
| 13 | Mexican  | Big Wong           | Expensive  | East Village       | 9:00         | 17:00        | 5              | TRUE          |
| 16 | Indian   | Wendy's            | Expensive  | Upper East Side    | 9:30         | 20:00        | 1              | FALSE         |

4. Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).
```sql
INSERT INTO reviews (restaurant_id, reviewer_name, review_text)
VALUES (1, 'Abby Thompson', 'Great experience, will come again!');
```
5. Delete all restaurants that are not good for kids.
```sql
DELETE FROM Restaurants
WHERE good_for_kids = False;
```
6. Find the number of restaurants in each NYC neighborhood.
```sql
SELECT neighborhood, COUNT(*) AS number_of_restaurants
FROM Restaurants
GROUP BY neighborhood;
```
| neighborhood      | number of restaurants |
|-------------------|-----------------------|
| Chelsea           | 80                    |
| East Village      | 85                    |
| Flushing          | 80                    |
| Gramercy          | 145                   |
| Little Italy      | 86                    |
| Lower East Side   | 92                    |
| Soho              | 80                    |
| Tribeca           | 88                    |
| Upper East Side   | 86                    |
| Upper West Side   | 84                    |
| West Village      | 94                    |

## Part 2: Social Media App

### Create table
1. [users table](data/users.csv)
- **id** (primary key: integer): An integer ranges from 1 to 1000, each restaurant matches with a unique id number.
- **email** (text): The user's email address.
- **password** (text): The user's password.
- **handle** (text): The user's username.
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    email TEXT NOT NULL UNIQUE,
    password TEXT NOT NULL,
    handle TEXT NOT NULL UNIQUE
);
```
2. [posts table](data/posts.csv)
- **id** (primary key: integer): An integer ranges from 1 to 1000, each restaurant matches with a unique id number.
- **user_id** (integer): The ID of the user who created the post, referencing the id in the users table. An integer ranges from 1 to 1000.
- **post_type** (text): Indicates whether the post is a 'message' or a 'story'.
- **content** (text): The text content of the post.
- **recipient_id** (integer): The ID of the recipient user, relevant for messages. For stories, this can be NULL.
- **visibility** (integer): Whether the post is visible (1) or invisible (0).
- **post_time** (daytime text): The timestamp when the post was created.
```sql
CREATE TABLE posts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    post_type TEXT NOT NULL CHECK(post_type IN ('message', 'story')),
    content TEXT NOT NULL,
    recipient_id INTEGER,
    visibility INTEGER NOT NULL DEFAULT 1,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(user_id) REFERENCES users(id),
    FOREIGN KEY(recipient_id) REFERENCES users(id)
);
```
### Queries
1. Register a new User.
```sql
INSERT INTO users (email, password, handle) VALUES ('user1001@db.com', 'password1001', 'user_1001');
```  
2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).
Let user 2 send a message to user 28.
```sql
INSERT INTO posts (user_id, post_type, content, recipient_id, visibility, created_at)
VALUES (2, 'message', 'Hello from user 2 to user 28', 28, 1, CURRENT_TIMESTAMP);
```  
3. Create a new Story by a particular User (pick any User for example).
```sql
INSERT INTO posts (user_id, post_type, content, visibility, created_at)
VALUES (4, 'story', 'This is a public story by user 4', 1, CURRENT_TIMESTAMP);
```  
4. Show the 10 most recent visible Messages and Stories, in order of recency.
```sql
SELECT * FROM posts WHERE visibility = 1 ORDER BY created_at DESC LIMIT 10;
```
| post_id | user_id | post_type | content                                                      | recipient_id | visibility | post_time          |
|---------|---------|-----------|--------------------------------------------------------------|--------------|------------|--------------------|
| 1726    | 920     | message   | Nulla neque libero, convallis eget, eleifend luctus, ultricies eu, nibh. | 731          | 0          | 2024/2/27 23:58    |
| 1561    | 771     | message   | Phasellus in felis.                                          | 347          | 0          | 2024/2/27 23:57    |
| 186     | 529     | story     | Nulla ut erat id mauris vulputate elementum.                 | null         | 1          | 2024/2/27 23:56    |
| 137     | 525     | message   | Aenean lectus.                                               | 192          | 0          | 2024/2/27 23:52    |
| 1038    | 202     | message   | Duis aliquam convallis nunc.                                 | 381          | 0          | 2024/2/27 23:51    |
| 803     | 268     | story     | Etiam pretium iaculis justo.                                 | null         | 0          | 2024/2/27 23:51    |
| 1366    | 530     | message   | Aliquam sit amet diam in magna bibendum imperdiet.           | 630          | 1          | 2024/2/27 23:50    |
| 1902    | 971     | message   | Nulla neque libero, convallis eget, eleifend luctus, ultricies eu, nibh. | 494          | 0          | 2024/2/27 23:48    |
| 149     | 780     | story     | Praesent blandit lacinia erat.                               | null         | 0          | 2024/2/27 23:47    |
| 1863    | 433     | message   | Fusce congue, diam id ornare imperdiet, sapien urna pretium nisl, ut volutpat sapien arcu sed augue. | 531          | 0          | 2024/2/27 23:46    |

5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
Assuming messages sent from user 10 to user 13:
```sql
SELECT * FROM posts
WHERE post_type = 'message' AND user_id = 10 AND recipient_id = 13 AND visibility = 1
ORDER BY created_at DESC
LIMIT 10;
```  
6. Make all Stories that are more than 24 hours old invisible.
```sql
UPDATE posts
SET visibility = 0
WHERE post_type = 'story' AND (JULIANDAY('now') - JULIANDAY(created_at)) * 24 > 24;
```  
7. Show all invisible Messages and Stories, in order of recency.
```sql
SELECT * FROM posts WHERE visibility = 0 ORDER BY created_at DESC;
```  
8. Show the number of posts by each User.
```sql  
SELECT user_id, COUNT(*) as post_count FROM posts GROUP BY user_id;
```  
9. Show the post text and email address of all posts and the User who made them within the last 24 hours.
```sql
SELECT p.content, u.email FROM posts p
JOIN users u ON p.user_id = u.id
WHERE (JULIANDAY('now') - JULIANDAY(p.created_at)) * 24 <= 24;
```  
10. Show the email addresses of all Users who have not posted anything yet.
```sql
SELECT email FROM users WHERE id NOT IN (SELECT DISTINCT user_id FROM posts);
```
