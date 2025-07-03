# Spring Redis Publish-Subscribe Implementation

A demonstration of the **Publish-Subscribe pattern** using **Spring Boot** and **Redis** as the message broker. This project showcases how multiple subscribers can listen to messages published to a Redis channel.

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Setup and Installation](#setup-and-installation)
- [Running the Application](#running-the-application)
- [How It Works](#how-it-works)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Output Example](#output-example)
- [Technologies Used](#technologies-used)

## Overview

This project demonstrates the **Publish-Subscribe messaging pattern** where:
- A **Producer** publishes messages to a Redis channel
- Multiple **Receivers** (subscribers) listen to the same channel
- Each message is delivered to all active subscribers
- Redis acts as the message broker facilitating the communication

## Architecture

```
┌─────────────┐    Publish    ┌───────────┐    Subscribe    ┌─────────────┐
│   Producer  │ ──────────────▶│   Redis   │◀─────────────── │  Receiver 1 │
└─────────────┘               │  Channel  │                 └─────────────┘
                              │PSChannel  │                 ┌─────────────┐
                              │           │◀─────────────── │  Receiver 2 │
                              │           │                 └─────────────┘
                              │           │                 ┌─────────────┐
                              └───────────┘◀─────────────── │  Receiver N │
                                                            └─────────────┘
```

## Features

- **Multiple Subscribers**: Creates 7 receiver instances, each listening to the same channel
- **Concurrent Message Processing**: Each receiver processes messages independently
- **Spring Boot Integration**: Leverages Spring's dependency injection and configuration
- **Redis Connection Management**: Configured connection factory for Redis
- **Message Listener Container**: Manages Redis message listeners
- **Prototype Bean Scope**: Each receiver is a separate instance with unique hash codes

## Prerequisites

- **Java 8+**
- **Maven 3.6+**
- **Docker** (for running Redis)
- **Redis Server** (via Docker container)

## Setup and Installation

### 1. Clone the Repository
```bash
git clone https://github.com/AnderssonProgramming/spring-redis-publish-subscribe.git
cd spring-redis-publish-subscribe
```

### 2. Start Redis Server
Start a Redis server using Docker on port 45000:
```bash
docker run --name some-redis -p 45000:6379 -d redis
```

### 3. Verify Redis is Running
```bash
docker ps --filter name=some-redis
```

You should see the Redis container running and mapping port 45000 to 6379.

## Running the Application

### 1. Build the Project
```bash
mvn clean compile
```

### 2. Run the Application
```bash
mvn spring-boot:run
```

### Alternative: Run with Java
```bash
mvn clean package
java -jar target/spring-redis-publish-subscribe-1.0-SNAPSHOT.jar
```

## How It Works

1. **Startup**: Spring Boot initializes the application and creates beans
2. **Receiver Creation**: The Producer creates 7 Receiver instances using ApplicationContext
3. **Listener Registration**: Each Receiver is registered as a message listener on "PSChannel"
4. **Message Publishing**: Producer sends 6 messages with 500ms intervals
5. **Message Consumption**: All receivers simultaneously receive each message
6. **Completion**: Application exits after sending all messages

### Key Components

- **PSRedisConnectionConfiguration**: Configures Redis connection factory
- **PSRedisTemplate**: Extends StringRedisTemplate for Redis operations
- **PSRedisListenerContainer**: Manages Redis message listeners
- **Producer**: Publishes messages and registers receivers
- **Receiver**: Subscribes to messages and processes them

## Project Structure

```
src/
├── main/
│   ├── java/
│   │   └── edu/eci/arsw/spring_redis_publish_subscribe/
│   │       ├── PSRedisPrimerAppStarter.java          # Main Spring Boot application
│   │       ├── connection/
│   │       │   ├── PSRedisConnectionConfiguration.java # Redis connection config
│   │       │   ├── PSRedisListenerContainer.java      # Message listener container
│   │       │   └── PSRedisTemplate.java               # Redis template
│   │       ├── producer/
│   │       │   └── Producer.java                      # Message producer
│   │       └── receiver/
│   │           └── Receiver.java                      # Message receiver
│   └── resources/
│       └── application.properties                     # Application configuration
└── test/
    └── java/
        └── edu/eci/arsw/spring_redis_publish_subscribe/
            └── AppTest.java                           # Unit tests
```

## Configuration

### application.properties
```properties
# Redis broker configuration
redis.broker.hostname=localhost
redis.broker.port=45000
```

### Key Configuration Points
- **Redis Host**: `localhost` (configurable via properties)
- **Redis Port**: `45000` (maps to container's 6379)
- **Channel Name**: `PSChannel` (hardcoded in Producer)
- **Message Count**: 6 messages
- **Receiver Count**: 7 instances
- **Send Interval**: 500ms between messages

## Output Example

When you run the application, you'll see output similar to:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.1.RELEASE)

2025-07-03 17:50:46.129  INFO --- [main] Starting PSRedisPrimerAppStarter...
2025-07-03 17:50:47.591  INFO --- [main] e.e.a.s.producer.Producer : Sending message... 1
2025-07-03 17:50:47.626  INFO --- [enerContainer-3] e.e.a.s.receiver.Receiver : 963768574. Received <Hello from Redis! Message 1>
2025-07-03 17:50:47.626  INFO --- [enerContainer-5] e.e.a.s.receiver.Receiver : 1712666248. Received <Hello from Redis! Message 1>
2025-07-03 17:50:47.626  INFO --- [enerContainer-6] e.e.a.s.receiver.Receiver : 1163404461. Received <Hello from Redis! Message 1>
...
```

Each message is received by all 7 receivers, showing their unique hash codes.

## Technologies Used

- **Spring Boot 2.3.1**: Application framework
- **Spring Data Redis**: Redis integration
- **Lettuce**: Redis Java client
- **Maven**: Build tool and dependency management
- **Docker**: Container platform for Redis
- **Redis**: In-memory data structure store used as message broker
- **Java 8**: Programming language
- **JUnit**: Testing framework

## Key Learning Points

1. **Publish-Subscribe Pattern**: Understanding asynchronous messaging
2. **Redis Integration**: Using Redis as a message broker
3. **Spring Boot Configuration**: Bean management and dependency injection
4. **Concurrent Processing**: Multiple listeners handling messages simultaneously
5. **Container Management**: Using Docker for infrastructure dependencies

## Troubleshooting

### Common Issues

1. **Redis Connection Failed**
   - Ensure Redis container is running: `docker ps --filter name=some-redis`
   - Check port mapping: Container should map 45000:6379

2. **Application Properties Not Found**
   - Ensure `application.properties` is in `src/main/resources/`
   - Verify the file contains Redis configuration

3. **Build Failures**
   - Run `mvn clean` to clear target directory
   - Ensure Java 8+ and Maven 3.6+ are installed

### Verification Commands

```bash
# Check Redis container
docker logs some-redis

# Test Redis connection
docker exec -it some-redis redis-cli ping

# Check application logs
mvn spring-boot:run | grep -E "(Sending|Received)"
```

---

This project demonstrates a practical implementation of the Publish-Subscribe pattern using modern Java and Redis technologies, providing a solid foundation for understanding asynchronous messaging systems.