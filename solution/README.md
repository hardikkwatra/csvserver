### Complete Giud:

# CSVServer Setup

This document provides the steps and commands executed to set up and run the `csvserver` application using Docker.

## Prerequisites

Ensure Docker and Docker Compose are installed on your machine.

## Steps

1. **Generate `inputFile`:**

   Create a bash script named `gencsv.sh` to generate the `inputFile`:

   ```bash
   #!/bin/bash
   start=$1
   end=$2
   for ((i=start; i<=end; i++))
   do
     echo "$i, $RANDOM"
   done > inputFile


   Make the script executable and run it:

   ```bash
   chmod +x gencsv.sh
   ./gencsv.sh 2 8
   ```

2. **Run the Docker Container:**

   Run the container image `infracloudio/csvserver:latest` in the background with the generated `inputFile`:

   ```bash
   docker run -d --name csvserver -v $(pwd)/inputFile:/csvserver/inputdata infracloudio/csvserver:latest
   ```

3. **Verify the Container is Running:**

   Check if the container is running:

   ```bash
   docker ps
   ```

   Check the container logs:

   ```bash
   docker logs csvserver
   ```

4. **Set Environment Variable and Run the Container Again:**

   Stop and remove the existing container:

   ```bash
   docker stop csvserver
   docker rm csvserver
   ```

   Run the container again, setting the `CSVSERVER_BORDER` environment variable:

   ```bash
   docker run -d --name csvserver -v $(pwd)/inputFile:/csvserver/inputdata -e CSVSERVER_BORDER=Orange -p 9393:9300 infracloudio/csvserver:latest
   ```

5. **Access the Application:**

   Open your browser and navigate to [http://localhost:9393](http://localhost:9393).

6. **Save the Solution:**

   - Save the Docker run command in a file named `part-1-cmd`:

     ```bash
     echo "docker run -d --name csvserver -v $(pwd)/inputFile:/csvserver/inputdata -e CSVSERVER_BORDER=Orange -p 9393:9300 infracloudio/csvserver:latest" > part-1-cmd
     ```

   - Generate the application output in a file named `part-1-output`:

     ```bash
     curl -o ./part-1-output http://localhost:9393/raw
     ```

   - Capture the container logs in a file named `part-1-logs`:

     ```bash
     docker logs csvserver >& part-1-logs
     ```

   - Commit and push the changes to your repository:

     ```bash
     git add .
     git commit -m "Completed Part I"
     git push origin main
     ```

## Part II: Docker Compose Setup

1. **Delete Any Running Containers:**

   ```bash
   docker stop csvserver
   docker rm csvserver
   ```

2. **Create `docker-compose.yaml`:**

   Create a `docker-compose.yaml` file for the setup from Part I:

   ```yaml
   version: '3'
   services:
     csvserver:
       image: infracloudio/csvserver:latest
       container_name: csvserver
       env_file:
         - csvserver.env
       ports:
         - "9393:9300"
       volumes:
         - ./inputFile:/csvserver/inputdata
   ```

3. **Create `csvserver.env`:**

   Create an environment variable file named `csvserver.env`:

   ```env
   CSVSERVER_BORDER=Orange
   ```

4. **Run the Application with Docker Compose:**

   Start the application using Docker Compose:

   ```bash
   docker-compose up -d
   ```

5. **Save the Solution:**

   - Commit and push the changes to your repository:

     ```bash
     git add docker-compose.yaml csvserver.env
     git commit -m "Completed Part II with docker-compose setup"
     git push origin main
     ```

## Part III: Adding Prometheus

1. **Delete Any Running Containers:**

   ```bash
   docker-compose down
   ```

2. **Update `docker-compose.yaml` to Add Prometheus:**

   Add the Prometheus container to the existing `docker-compose.yaml`:

   ```yaml
   version: '3'
   services:
     csvserver:
       image: infracloudio/csvserver:latest
       container_name: csvserver
       env_file:
         - csvserver.env
       ports:
         - "9393:9300"
       volumes:
         - ./inputFile:/csvserver/inputdata

     prometheus:
       image: prom/prometheus:v2.45.2
       container_name: prometheus
       ports:
         - "9090:9090"
       volumes:
         - ./prometheus.yml:/etc/prometheus/prometheus.yml
   ```

3. **Create Prometheus Configuration File:**

   Create a Prometheus configuration file named `prometheus.yml`:

   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: 'csvserver'
       static_configs:
         - targets: ['csvserver:9300']
   ```

4. **Run the Application with Docker Compose:**

   Start the application with Docker Compose:

   ```bash
   docker-compose up -d
   ```

5. **Check Prometheus:**

   - Open Prometheus at [http://localhost:9090](http://localhost:9090).
   - In the query box, type `csvserver_records`, click Execute, and switch to the Graph tab.

6. **Save the Solution:**

   - Commit and push the changes to your repository:

     ```bash
     git add docker-compose.yaml prometheus.yml
     git commit -m "Completed Part III with Prometheus integration"
     git push origin main
     ```

### Supporting Screenshots:

## Part I:
![Supporting Screenshot](https://github.com/hardikkwatra/csvserver/blob/44be75c66fa2a9de049d5a031e3d140252c2a7f3/solution/SupportingScreenshot1.png)

## Part II & III:
![Supporting Screenshot](https://github.com/hardikkwatra/csvserver/blob/44be75c66fa2a9de049d5a031e3d140252c2a7f3/solution/SupportingScreenshot2.png)

![Supporting Screenshot](https://github.com/hardikkwatra/csvserver/blob/44be75c66fa2a9de049d5a031e3d140252c2a7f3/solution/SupportingScreenshot3.png)




