Here's the entire setup guide:

# CSVServer Setup

This document provides the steps and commands executed to set up and run the `csvserver` application using Docker.

## Prerequisites
Ensure Docker and Docker Compose are installed on your machine.

## Steps

### 1. Generate `inputFile`

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

### 2. Run the Docker Container

Run the container image `infracloudio/csvserver:latest` in the background with the generated `inputFile`:

```bash
docker run -d --name csvserver -v $(pwd)/inputFile:/csvserver/inputdata infracloudio/csvserver:latest
```

### 3. Verify the Container is Running

Check if the container is running:

```bash
docker ps
```

Check the container logs:

```bash
docker logs csvserver
```

### 4. Set Environment Variable and Run the Container Again

Stop and remove the existing container:

```bash
docker stop csvserver
docker rm csvserver
```

Run the container again, setting the `CSVSERVER_BORDER` environment variable:

```bash
docker run -d --name csvserver -v $(pwd)/inputFile:/csvserver/inputdata -e CSVSERVER_BORDER=Orange -p 9393:9300 infracloudio/csvserver:latest
```

### 5. Access the Application

Open your browser and navigate to [http://localhost:9393](http://localhost:9393).

### 6. Save the Solution

Save the Docker run command in a file named `part-1-cmd`:

```bash
echo "docker run -d --name csvserver -v $(pwd)/inputFile:/csvserver/inputdata -e CSVSERVER_BORDER=Orange -p 9393:9300 infracloudio/csvserver:latest" > part-1-cmd
```

Generate the application output in a file named `part-1-output`:

```bash
curl -o ./part-1-output http://localhost:9393/raw
```

Capture the container logs in a file named `part-1-logs`:

```bash
docker logs csvserver >& part-1-logs
```

### 7. Commit and Push the Changes to Your Repository

```bash
git add .
git commit -m "Completed Part I"
git push origin main
```
