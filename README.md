# Exploring pgvector as a Vector Database 
This demo is using Docker to containerize PostgreSQL with pgvector extension. This demo is created to document my exploration of PgVector. I'm open to any suggestion and correction.

## Installation
You can install PostgreSQL in any way. You can find the complete official guide [here](https://github.com/pgvector/pgvector). 
I use Docker container to deploy containerized PostgreSQL locally.
If you want to follow this installation guide for pgvector, make sure you already have Docker installed on your device.
1. We can install PostgreSQL easily by pulling PostgreSQL image. You can use any version for the PostgreSQl. I use version 16.
    ```
    docker pull pgvector/pgvector:pg16
    ```
2. Run the container with the downloaded image and initialize your superuser password in the environment variable. By default, the username will be <i>postgres</i>.
    ```
    docker run -d --name pgvector -e POSTGRES_PASSWORD=<YOUR_PASSWORD> -p 5432:5432 pgvector/pgvector:pg16
    ```
3. Wait until deployment is complete. You can access the deployed container with SSH connection.
    ```
    docker exec -it pgvector sh
    ```
4. You can run this command to access PostgreSQL within the deployed container with superuser access.
    ```
    psql --user postgres
    ``` 

## Prepare the Data
1. Create your database. I use CLI here. You can use PgAdmin or any other similar tools.
    ```sql
    CREATE DATABASE pgvector_sandbox;
    ```
2. Since we want to use PgVector, we need to enable the extension first. Enable the extension by running the following command. This command only running in the current database.
    ```sql
    CREATE EXTENSION vector;
    ```
3. Download the dataset. This demo use [IMDB Movies Dataset from Kaggle by Shankhdhar](https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows).
4. Create a table following the schema of the dataset. You can use PgAdmin to import csv file. But I'll use to CLI for now.
    ```sql
    CREATE TABLE movies (
        poster_link VARCHAR(255),
        series_title VARCHAR(255),
        released_year INT,
        certificate VARCHAR(255),
        runtime VARCHAR(255),
        genre VARCHAR(255),
        imdb_rating FLOAT,
        overview VARCHAR(255),
        meta_score FLOAT,
        director VARCHAR(255)
    );
    ```
5. We will insert the data using Python's sqlalchemy library. 
    ```py
    import psycopg2
    import pandas as pd
    from sqlalchemy import create_engine 

    # Adjust accordingly
    conn_string = 'postgres://<USERNAME>:<PASSWORD>@host/<DATABASE_NAME>'

    # create db connection
    db = create_engine(conn_string) 
    conn = db.connect() 

    # import csv file as dataframe
    file_path = "<FILE_PATH>"
    df = pd.read_csv(file_path)

    # insert to db
    df.to_sql('data', con=conn, if_exists='replace', index=False)
    conn = psycopg2.connect(conn_string) 
    conn.autocommit = True
    cursor = conn.cursor() 
    
    query = '''select * from data;'''
    cursor.execute(query) 
    for i in cursor.fetchall(): 
        print(i) 
    
    conn.close() 
    ```
6. You ready to start the demo! Please refer to [similar_movies.ipynb](./similar_movies.ipynb) to continue.

</br>

References:
- [Manhattan Distance - Datacamp](https://www.datacamp.com/tutorial/manhattan-distance) 
- [L2, Cosine Distance, and Inner Product - Ziliz](https://zilliz.com/blog/similarity-metrics-for-vector-search)
- [Embedding in NLP - Pilehvar, Camacho-Collados ](https://books.google.co.id/books?hl=en&lr=&id=U90MEAAAQBAJ&oi=fnd&pg=PP2&dq=vector+embeddings&ots=rw4l0A6k4G&sig=gIsTMDXlSMql3EFGc63q-vRS_IY&redir_esc=y#v=onepage&q=vector%20embeddings&f=false)