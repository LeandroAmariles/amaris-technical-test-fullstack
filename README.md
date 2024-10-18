# Angular and Spring project with Docker

This project contains a frontend developed in Angular and a backend implemented in Spring Boot. Both services are configured to be run in Docker containers using Docker Compose.

## Table of Contents

1. [Requirements](#requirements)
   Project Structure](#project-structure) 3.
3. [Docker Configuration](#docker-configuration)
4. [Usage Instructions](#usage-instructions)
5. [Backend Endpoints](#endpoints-backend)


## Requirements

- [Docker](https://www.docker.com/get-started) (version 20.10 or higher)
- Docker Compose](https://docs.docker.com/compose/) (version 1.27 or higher)
## Project Structure

The project consists of two components a frontend made with Angular in its version 17 with bootstrap.
For the backend we used Spring version 3.3.4 and Java 17.

## Docker configuration

### Dockerfile of the Frontend (Angular)

```dockerfile
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build --dev
FROM nginx:alpine
COPY --from=build /app/dist/* /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD [“nginx”, “-g”, “daemon off;”]
```

# Dockerfile for the Backend
````dockerfile
FROM maven:3.8.4-openjdk-17 AS build
LABEL authors=“Leandro”
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests
FROM tomcat:10.1-jdk17
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=build /app/target/*.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD [“catalina.sh”, “run”]
````

# Docker-compose
````yaml
version: '3.8
services:
  backend:
    build:
      context: amaris-technical-test-backend
    ports:
      - “8080:8080”
    networks:
      - app-amaris-network
  frontend:
    build:
      context: amaris-technical-test-frontend
    ports:
      - “4200:80”
    networks:
      - app-amaris-network
networks:
  app-amaris-network:
    driver: bridge
````
## Usage Instructions

1. Clone this repository on your local machine:

   ````bash
   git clone https://github.com/LeandroAmariles/amaris-technical-test-fullstack.git
   cd amaris-technical-test-fullstack
   ````
   Additional Repositories
    * Backend: https://github.com/LeandroAmariles/amaris-technical-test-backend.git
    * Frontend: https://github.com/LeandroAmariles/amaris-technical-test-frontend.git
   

2. Build and run the containers with Docker Compose:

    ````bash
    docker-compose up --build
    ````

3. Access the frontend at http://localhost:4200.
4. Access the backend at http://localhost:8080.



## Backend endpoints

The backend has a circuit breaker, in order to avoid crashes when the client to be consumed is not available.

### 1. Get employee list
- **Method**: `GET`.
- URL**: `/get-all-employees"`.
- **Description**: Gets the complete list of employees.
- **Response**:
    - **Code 200**: Returns a list of employees.
  
### 2. Get employee
- **Method**: `GET`.
- **URL**: `/get-employee/{id}"`.
- **Description**: Gets an employee by id.
- **Response**:
    - **Code 200**: Returns an employee.

### 3. Calculate employee annual salary
- **Method**: `GET`.
- **URL**: `/get-employee-annual-salary/{id}"`.
- **Description**: Gets an employee by id and calculates annual salary for him/her.
- **Response**:
    - **Code 200**: Returns an employee.

