# Attendance-API Setup 


***
## Table of Contents 
+ [Introduction](#Introduction)
+ [Key Components](#key-components)
+ [Architecture](#architecture)
+ [Pre requisites](#pre-requisites)
+ [System Requirements](#system-requirements)
+ [Dependencies](#dependencies)
+ [API Setup](#api-setup)
+ [Security](#best-practices-for-api-security)
+ [Troubleshoot](#troubleshoot)
+ [Contact Information](#contact-information)
+ [References](#references)

***
## Introduction
Attendance API is a Python-based microservice designed to handle attendance-related transactions in the context of the OT-Microservices ecosystem. This microservice serves as a central component for managing attendance records and transactions, providing a set of RESTful APIs for integration with other services.

***
## Key Components
| Components               | Description                                                                                                     |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------|
| Flask Web Framework         | The API is built using Flask, a lightweight and flexible web framework for Python, simplifying RESTful API development. |
| PostgreSQL Database         | PostgreSQL serves as the primary database for storing attendance records, ensuring data integrity and facilitating efficient querying. |
| Redis Cache Management      | Redis is employed as a cache management middleware, enhancing performance and responsiveness by storing API responses for quick retrieval. |
| Prometheus and OpenTelemetry Metrics | The API supports monitoring and observability through integration with Prometheus and OpenTelemetry, providing metrics for performance analysis. |
| Liquibase                    | Liquibase acts as a version control system for database schema, tracking changes over time.                          |
| Swagger Integration          | Swagger is integrated for API documentation, making it easy for developers to test and interact with API endpoints. |

***
## Flow Diagram 
<img width="802" alt="Screenshot 2024-01-12 at 1 04 42 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/c8e7795b-a96e-4570-b47a-cb0e2fda8b4f">

***
## Getting Started
### Pre-Requisites
  Before running the Attendance API, certain pre-requisites need to be configured:
* PostgreSQL Database
* Redis
* Poetry (Package Manager)
* Liquibase (Database Schema Version Control)

### System Requirements:
 | Requirement | Recommendation |
 |-------------|----------------|
 | Processor/Instance Type |  Dual-Core/t2.medium |
 | RAM (Memory) |	4 Gigabytes  |
 | Disk Space | 16 GB or 2GB per core |
 | OS Required (Linux Distributions) | Ubuntu 22.04 LTS, Debian, or CentOS 7/8 |

 ### Ports
   |  Inbound Traffic|	Description  |
   |---------|---------|
   | 5432	| Postgresql port|
   | 6379 | Redis port | |
   | 8080 | Attendance API port |
   | 22 | Port 22 to establish an SSH connection to an EC2 |

***
## Dependencies:
|  Buildtime Dependency|	Version 
   |---------|---------|
   | Make	| 4.2.1|
   | Pip   | 22.0.2|
   | Poetry | 1.7.1 |
   | Python | 3.11 or above |

|   Runtime Dependency|	Version 
   |---------|---------|
   | Redis	| 6.0.16|
   | Postgresql   | 14.10|
   | Liquibase | 4.25.1 |
   | Java | 11.0.21 |

*** 
## API Setup
### 1. Clone Repository:
```shell
    git clone https://github.com/OT-MICROSERVICES/attendance-api.git
```

### 2. API Setup Dependencies:

1) **Install Make:**
```shell
sudo apt update
sudo apt upgrade -y
sudo apt install make -y 
```
***
2) **Pip:**
```shell
sudo apt install python3-pip
```
*** 
3) **Python3.11**
* Prior to poetry installation, make sure to install Python 3.11, as Poetry is compatible with Python versions 3.11 or above. You can find the installation link [here](https://vegastack.com/tutorials/how-to-install-python-3-11-on-ubuntu-22-04/)

***
3) **Poetry:**

* Install poetry and add Path to your shell configuration file. 
```shell
curl -sSL https://install.python-poetry.org | python3 -
```
```shell
export PATH="/home/ubuntu/.local/bin:$PATH"
```

<img width="960" alt="Screenshot 2024-01-19 at 1 28 04 AM" src="https://github.com/avengers-p7/Documentation/assets/156056349/cd0a1807-5864-4ae5-92e7-c9aca68e664f">

*** 
4) **PostgreSQL**

* For data storage and cache you need to setup postgreSQL and redis either as a container or locally. To setup locally you may use the following commands:

```shell
sudo apt update
sudo apt install postgresql
```

<img width="1028" alt="Screenshot 2024-01-12 at 2 47 15 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/46a21c38-8ef8-4d57-bafa-b6a76649f310">

* Create Database and user
```shell
sudo -u postgres psql
```
```shell
CREATE DATABASE attendance_db;
``` 
> NOTE: Remember to alter the superuser password based on the config.yaml file. Otherwise, the connection would not be established. 

* To alter the password you may use the following command
```shell
ALTER USER postgres WITH PASSWORD 'password';
```
***
5) **Redis**
```shell
sudo apt install redis-server
```
<img width="1107" alt="Screenshot 2024-01-12 at 2 48 08 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/8ae0c0cc-0edd-436b-a6b4-9ee5bb3b75ed">

***
6) **Liquibase**
   
Liquibase helps in versioning and managing database schema changes in a flexible and automated way.

* Ensure Java is installed as a prerequisite for Liquibase. You can use this link [here](https://www.java.com/en/download/help/linux_install.html#Java%20for%20Linux%20Platforms)
 to install Java.
  
```shell
wget -O- https://repo.liquibase.com/liquibase.asc | gpg --dearmor > liquibase-keyring.gpg && \
cat liquibase-keyring.gpg | sudo tee /usr/share/keyrings/liquibase-keyring.gpg > /dev/null && \
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/liquibase-keyring.gpg] https://repo.liquibase.com stable main' | sudo tee /etc/apt/sources.list.d/liquibase.list
``` 
```shell
sudo apt-get update
sudo apt-get install liquibase
``` 
<img width="1150" alt="Screenshot 2024-01-12 at 2 53 29 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/f1bf824c-f540-4703-b254-ec87ccecef52">

* To establish the connect between liquibase and postgres you need to make changes in liquibase.properties file. This is how you may do it.

<img width="610" alt="Screenshot 2024-01-19 at 5 10 26 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/c536a7bb-480b-4a3d-bf18-f8a733932189">

***
### 3.  Building and running:
1) **Run-Migrations**
   
* Ensure the PostgreSQL database is up and running, then liquibase to keep track of database schema updates.
```shell
make run-migrations
``` 

<img width="1383" alt="Screenshot 2024-01-11 at 10 07 06 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/9f9f629a-6929-4480-a489-19582f691e3f">

***
2) **Make build**
* To successfully run the make command, ensure that your Makefile is configured as follows

<img width="1125" alt="Screenshot 2024-01-19 at 5 12 26 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/34f1e782-868f-4780-85fb-de59b8583564">


* To ensure Poetry runs without errors, install psycopg2 is installed . Additionally, you may need to install python3.11-dev to add development packages and files. 
```shell
sudo apt-get install python3.11-dev
```
```shell
poetry add psycopg2-binary
sudo apt-get install libpq-dev
```

* Execute the build command to install necessary dependencies using Poetry
```shell
make build
```
* To inspect the installed dependencies by Poetry, you can use the command `poetry show`.

***
3)  **Run Application**
   
* After installing the required dependencies in the Poetry shell environment for running the API, it's advisable to install Gunicorn directly within the virtual environment. Execute the following command to achieve this:

```shell
poetry shell
```
```shell
poetry add gunicorn
```
<img width="773" alt="Screenshot 2024-01-19 at 5 20 37 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/334181e1-f5af-4464-8886-b5f8539c4fbd">


*  Finally, to Load your application on gunicorn server and make it available for incoming HTTP request you may run this following command in the virtual shell
```shell
gunicorn app:app --log-config log.conf -b 0.0.0.0:8080
```
* Once the setup is done, you can access the swagger page on http://localhost:8080/apidocs/ and track your endpoints. The page would look something like this 

<img width="1344" alt="Screenshot 2024-01-11 at 10 46 38 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/5f41585e-65f1-4e2e-81bf-ec72377a65b9">

* You may further test your API methods using swagger. If test cases are passed, results should look like this:

<img width="1317" alt="Screenshot 2024-01-11 at 10 47 56 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/3537c15b-df10-436b-bffa-134a1baa8cbe">


<img width="1302" alt="Screenshot 2024-01-11 at 10 48 42 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/c254ea89-3267-4f29-a387-c3f8a421826a">


<img width="1308" alt="Screenshot 2024-01-11 at 10 49 25 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/25ec75f6-7680-49e9-899b-de8f6ed55504">

***
## API Endpoints 

| **Endpoint**                     | **API Method** | **Description**                                                                                      |
|----------------------------------|------------|------------------------------------------------------------------------------------------------------|
| /metrics                         | GET        | Health check and performance metrics for application                    |
| /apidocs                         | GET        | Swagger endpoint for the application API documentation                       |
| /api/v1/attendance/create        | POST       | Data creation endpoint which accepts certain JSON body to add records in database     |
| /api/v1/attendance/search        | GET        | For searching records using the params in the URL                                  |
| /api/v1/attendance/search/all    | GET        | Endpoint for searching each data across the system             |                                
| /api/v1/attendance/health        | GET        | Provides shallow health check information about application health and readiness        |
| /api/v1/attendance/health/detail | GET        | Provides detailed health check about dependencies as well like - PostgresSQL and Redis |

***
## Best Practices for API security

1. **Always use TLS:** Use HTTPS to encrypt data transmitted between clients and the API server. This helps prevent man-in-the-middle attacks. Obtain and install an SSL certificate for your domain.

2. **Use OAuth for SSO:** Nearly every app will need to associate some private data with a single person. That means user accounts, and that means logging in and logging out. SSO allows users to verify themselves with a trusted third party (e.g., Google, Microsoft Azure, AWS) via token exchange to access a resource.

***
##  Troubleshoot
1. **Problem -** The error suggests that psycopg2 requires pg_config to be available in the system. pg_config is a utility that is part of the PostgreSQL installation and is necessary for building Python packages that depend on it.

   **Solution -** Installed psycopg2 using poetry along with python3-dev libpq-dev.
<img width="1401" alt="Screenshot 2024-01-19 at 2 53 54 AM" src="https://github.com/avengers-p7/Documentation/assets/156056349/2e5908f1-b7e3-4b4c-bdc5-90928ddbdf7c">

2. **Problem -** There was a need to create and manage mocks in pytest for testing, but this functionality wasn't built into pytest itself.
   
   **Solution -** The problem was resolved by installing the pytest-mock plugin using the command: `pip install pytest-mock`. The plugin provides a "mocker" fixture for creating and managing mocks in test functions.


<img width="1186" alt="Screenshot 2024-01-11 at 11 16 31 PM" src="https://github.com/avengers-p7/Documentation/assets/156056349/97e0f816-7a19-4bbf-b1af-c35bf95a1b26">


3. **Problem -** Encountered issues in loading application through gunicorn.
   
   **Solution -** Resolved problem by modifying config.yaml to fix connectivity issues with PostgreSQL and Redis.

4. **Problem -** Encountered an Error13 while running make build as Poetry struggled to install dependencies without a virtual environment.

   **Solution -** Resolved compatibility issues by utilizing Python 3.11, then used a virtual environment to successfully install all required dependencies specified in the pyproject.toml file.

   
***
## Contact Information

|Vidhi Yadav                     | vidhi.yadhav.snaatak@mygurukulam.co                                                                                      
|---------------------------------|------------------------------------------------------------|

***
## References

|     Description                  | References  
| ---------------------------------| ------------------------------------------------------------------- |
|     Error Handling               | https://bobbyhadz.com/blog/disable-missing-module-docstring-pylint
|     Attendance Documentation     | https://github.com/OT-MICROSERVICES/attendance-api/tree/main        
|     Pytest mock                  | https://pytest-with-eric.com/introduction/fixture-mocker-not-found/)https://pytest-with-eric.com/introduction/fixture-mocker-not-found/
|     psycopg2 Requirement         | https://stackoverflow.com/questions/71195823/poetry-python-how-to-install-psycopg2-with-postgres-running-from-docker
|   Poetry Virtual Env (Error13)   | https://ask.replit.com/t/cant-install-poetry-using-pip-on-a-blank-repl/45586
| Postgres Installation            | https://github.com/avengers-p7/Documentation/blob/main/OT%20Micro%20Services/Software/PostgresSQL.md
| Redis Installation               | https://redis.io/docs/install/install-redis/install-redis-on-linux/
| Install Poetry                   | https://python-poetry.org/docs/#installing-with-the-official-installer

