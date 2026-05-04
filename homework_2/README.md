**TASK 1:
To run Task 1:
task1_download_and_upload_dataset()

Expected output:
On first run:
- Dataset is downloaded locally
- dataset is uploaded to S3
On subsequent runs:
- The script detects the dataset in S3
- download and upload steps are skipped

Sequence of functions/brief description:
1. s3_object_exists(bucket_name, s3_key)
- checks whether the dataset file already exists in the specificed S3 bucket using a head_object request
- returns True if the file exists
- returns False if the file does not exist

2. download_movielens(local_path)
- Donwloads the MovieLens 1M dataset from the official URL and saves it locally
- creates the local directory if it does not exist
- writes the dataset as a .zip file to disk

3. upload_to_s3(local_path, bucket_name, s3_key)
- Uploads the locally downloaded dataset file to the specified S3 bucket and key

4. task1_download_and_upload_dataset()
- Main pipeline function that conducts the workflow:
- if it exists --> skips download and upload
- if it does not exist --> downloads the dataset and uploads it to S3

**TASK 2
To run Task 2:
task2_create_pre1980_embeddings()

Expected output:
On first run:
- MovieLens dataset is extracted (if not already extracted)
- Movies released in or before 1980 are filtered
- Text representations of movies are created
- BERT embeddings are generated for each movie
- Filtered movies and embeddings are saved locally
- Intermediate results are uploaded to S3
On subsequent runs:
- The script detects the dataset in S3
- download and upload steps are skipped

Sequence of functions/brief description:
1. s3_intermediate_results_exist()
- checks whether the filtered movie data and embeddings already exist in the specified S3 bucket
- returns True if both intermediate files exist
- returns False otherwise

2. extract_movielens_dataset(zip_path)
- extracts the MovieLens 1M dataset from the local zip file
- skips extraction if the dataset has already been extracted

3. load_movies(data_path)
- loads the movies.dat file into a pandas DataFrame
- assigns column names: MovieID, Title, Genres

4. filter_movies_pre1980(movies)
- extracts the year from the movie title
- filters movies released in or before 1980 (inclusive)
- returns the filtered dataset

5. create_movie_text(movies)
- creates a text representation for each movie
- combines title, genres, and release year into a single string
- prepares input for the BERT model

6. load_bert_model()
- loads the DistilBERT tokenizer and encoder model
- moves the model to the appropriate device (CPU or GPU)

7. bert_embed(texts, tokenizer, encoder)
- generates embeddings for movie text using DistilBERT
- tokenizes input text with padding and truncation
- extracts the CLS token representation from the model output
- normalizes embeddings using L2 normalization
- processes data in batches for efficiency

8. save_task2_outputs(pre1980_movies, embeddings)
- saves the filtered movie dataset as a CSV file
- saves the embeddings as a NumPy array file (.npy)

9. upload_task2_outputs_to_s3(movies_path, embeddings_path)
- uploads the filtered dataset and embeddings to the specified S3 bucket

10. task2_create_pre1980_embeddings()
- main pipeline function that conducts the workflow:
- if intermediate results exist in S3 → skips computation
- if not:
    - extracts dataset
    - loads and filters movies
    - creates text inputs
    - generates embeddings
    - saves outputs locally
    - uploads results to S3
 
**TASK 3
To run Task 3:
task3_generate_recommendations_pre1980()

Expected output:
- Ratings and user data are loaded
- Pre-1980 movies and embeddings from Task 2 are loaded
- A cold user and a top user are identified
- 5 movie recommendations are generated for each user
- Results are saved locally as a JSON file
- Results are uploaded to S3

Sequence of functions/brief description:
1. load_ratings(data_path)
- loads the ratings.dat file into a pandas DataFrame
- assigns column names: UserID, MovieID, Rating, Timestamp

2. load_users(data_path)
- loads the users.dat file into a pandas DataFrame
- assigns column names: UserID, Gender, Age, Occupation, ZipCode

3. load_task2_outputs()
- loads the filtered pre-1980 movies dataset from a CSV file
- loads the corresponding BERT embeddings from a NumPy file

4. choose_top_user(ratings)
- calculates the number of interactions per user
- identifies users in the top 5% by number of interactions
- randomly selects one top user

5. recommend_for_cold_user(pre1980_movies, ratings)
- generates recommendations for a cold user with no interaction history
- uses a popularity based approach based on average rating and number of ratings
- filters to pre-1980 movies only
- returns the top 5 recommended movies

6. recommend_for_top_user(user_id, pre1980_movies, embeddings, ratings)
- generates recommendations for a top user using BERT embeddings
- identifies movies the user rated highly (rating >= 4)
- computes a user profile embedding by averaging embeddings of liked movies
- calculates similarity scores between the user profile and all candidate movies
- excludes movies already seen by the user
- returns the top 5 most similar movies

7. summarize_top_user(user_id, num_interactions, ratings, users)
- retrieves user demographic and interaction data
- calculates average rating and last interaction time
- returns a summary of the top user

8. summarize_cold_user()
- creates a placeholder summary for a cold user
- indicates that no interaction data is available

9. save_task3_results(results)
- saves the recommendation results as a JSON file locally

10. upload_task3_results_to_s3(local_path)
- uploads the JSON results file to the specified S3 bucket

11. task3_generate_recommendations_pre1980()
- main function that conducts the workflow:
    - loads dataset and intermediate outputs
    - selects users
    - generates recommendations
    - saves and uploads results to S3
 
**TASK 4
To run Task 4:
task4_create_full_embeddings()
task4_generate_recommendations_full()

Expected output:
On first run:
- MovieLens dataset is extracted (if not already extracted)
- Full movie dataset is prepared (no filtering applied)
- Text representations of all movies are created
- BERT embeddings are generated for all movies
- Full movie dataset and embeddings are saved locally
- Intermediate results are uploaded to S3
- Recommendations are generated for both cold user and top user using the full dataset
- Results are saved locally as a JSON file
- Results are uploaded to S3
On subsequent runs:
- The script detects that full dataset embeddings already exist in S3
- Embedding generation step is skipped
- Recommendations are still generated using existing embeddings

Sequence of functions/brief description:
1. full_intermediate_results_exist()
- checks whether full dataset movie data and embeddings already exist in the specified S3 bucket
- returns True if both files exist
- returns False otherwise

2. prepare_full_movies(movies)
- prepares the full movie dataset without filtering by year
- extracts the year from movie titles
- creates text representations for each movie
- returns the processed dataset

3. save_task4_embeddings_outputs(full_movies, embeddings)
- saves the full movie dataset as a CSV file
- saves the embeddings as a NumPy array file (.npy)

4. upload_task4_embeddings_outputs_to_s3(movies_path, embeddings_path)
- uploads the full dataset and embeddings to the specified S3 bucket

5. task4_create_full_embeddings()
- main pipeline function for embedding generation:
- checks if embeddings already exist in S3
- if not:
    - loads dataset
    - prepares full movie data
    - generates BERT embeddings
    - saves outputs locally
    - uploads results to S3
  
6. load_task4_outputs()
- loads the full movie dataset and embeddings from local files

7. task4_generate_recommendations_full()
- main pipeline function for generating recommendations:
- loads ratings and user data
- loads full dataset embeddings
- selects a top user
- generates recommendations for cold user using popularity-based method
- generates recommendations for top user using embedding similarity
- saves results locally as a JSON file
- uploads results to S3


AI Usage Note: 
(1) tool(s) used: ChatGPT
(2) the key prompt(s): I went back to my lab3 assignment because I was having issues pip installing pytorch and transformers. I asked chat "on this line !pip install -U transformers datasets evaluate accelerate torch I get this error [pasted error] I had to comment out any use of torch and transformers because of this but why is this not working"
(3) what you changed and how you verified the results: ChatGPT suggested I do !pip cache purge
!pip install --no-cache-dir -U transformers datasets evaluate accelerate
!pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cpu

and I verified the results by running my lab again with the torch and transformers uncommented, and it worked. I used this --no-cache-dir in my homework 2 work.