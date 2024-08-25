# Exploring pgvector with LangChain
This demo is using Docker to containerize PostgreSQL with pgvector extension.

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
    docker run -d --name pgvector -e POSTGRES_PASSWORD=<YOUR_PASSWORD> pgvector/pgvector:pg16
    ```
3. Wait until deployment is complete. You can access the deployed container with SSH connection.
    ```
    docker exec -it pgvector/pgvector sh
    ```
4. You can run this command to access PostgreSQL within the deployed container with superuser access.
    ```
    psql --user postgres
    ``` 
