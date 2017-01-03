[![Build Status](https://travis-ci.org/ThoughtWorksInc/voter-service.svg?branch=master)](https://travis-ci.org/ThoughtWorksInc/voter-service)

# Voter Service

## Introduction

Spring Boot RESTful API µService, backed by MongoDB, used for DevOps training and testing. The Voter µService exposes several endpoints, listed below. API users can review a list candidates, submit a vote, view voting results, and so forth. API users can also create random voting data, calling the `/simulation` endpoint.

## Quick Start for Local Development

The Voter service requires MongoDB to be pre-installed and running locally, on port `27017`. To clone, build, test, and run the Voter µService, locally:

```bash
git clone https://github.com/ThoughtWorksInc/voter-service.git
cd voter-service
./gradlew clean cleanTest build
java -jar build/libs/voter-service-0.2.0.jar
```

## Service Endpoints

By default, the service runs on `localhost`, port `8099`. By default, the service looks for MongoDB on `localhost`, port `27017`.

- Create Random Sample Data (GET): <http://localhost:8099/simulation>
- List Candidates (GET): <http://localhost:8099/candidates>
- Submit Vote (POST): <http://localhost:8099/votes>
- View Voting Results (GET): <http://localhost:8099/results>
- View Total Votes (GET): <http://localhost:8099/results/votes>
- View Winner(s) (GET): <http://localhost:8099/winner>
- View Winning Vote Count (GET): <http://localhost:8091/winner/votes>
- Service Info (GET): <http://localhost:8099/info>
- Service Health (GET): <http://localhost:8099/health>
- Service Metrics (GET): <http://localhost:8099/metrics>
- Other [Spring Actuator](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready) endpoints include: `/mappings`, `/env`, `/configprops`, etc.
- Other [HATEOAS](https://spring.io/guides/gs/rest-hateoas) endpoints for `/votes` include: DELETE, PATCH, PUT, page sort, size, etc.

## Voting

Submitting a new vote, requires an `HTTP POST` to the `/votes` endpoint, as follows:

HTTPie

```text
http POST http://localhost:8099/votes candidate="Hillary Clinton"
```

cURL

```text
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{ "candidate": "Hillary Clinton" }' \
  "http://localhost:8099/votes"
```

wget

```text
wget --method POST \
  --header 'content-type: application/json' \
  --body-data '{ "candidate": "Hillary Clinton" }' \
  --no-verbose \
  --output-document - http://localhost:8099/votes
```

## Sample Output

Using [HTTPie](https://httpie.org/) command line HTTP client.

`http http://localhost:8099/candidates`

```json
{
    "candidates": [
        "Chris Keniston",
        "Darrell Castle",
        "Donald Trump",
        "Gary Johnson",
        "Hillary Clinton",
        "Jill Stein"
    ]
}
```

`http http://localhost:8099/simulation`

```json
{
    "message": "random simulation data created"
}
```

`http http://localhost:8099/results`

```json
{
    "results": [
        {
            "candidate": "Gary Johnson",
            "votes": 20
        },
        {
            "candidate": "Hillary Clinton",
            "votes": 15
        },
        {
            "candidate": "Donald Trump",
            "votes": 11
        },
        {
            "candidate": "Jill Stein",
            "votes": 8
        },
        {
            "candidate": "Chris Keniston",
            "votes": 3
        },
        {
            "candidate": "Darrell Castle",
            "votes": 2
        }
    ]
}
```

`http http://localhost:8099/results/votes`

```json
{
    "votes": 59
}
```

`http http://localhost:8099/winner`

```json
{
    "results": [
        {
            "candidate": "Gary Johnson",
            "votes": 20
        }
    ]
}
```

`http http://localhost:8099/winner/votes`

```json
{
    "votes": 20
}
```

`http POST http://localhost:8099/votes vote="Jill Stein"`

```json
{
    "_links": {
        "self": {
            "href": "http://localhost:8099/votes/58279bda909a021142712fe7"
        },
        "vote": {
            "href": "http://localhost:8099/votes/58279bda909a021142712fe7"
        }
    },
    "vote": "Jill Stein"
}
```

## Continuous Integration

The project's source code is continuously built and tested on every commit to [GitHub](https://github.com/ThoughtWorksInc/voter-service), using [Travis CI](https://travis-ci.org/ThoughtWorksInc/voter-service). If all unit tests pass, the resulting Spring Boot JAR is pushed to the `artifacts` branch of the [ThoughtWorksInc/voter-service](https://github.com/ThoughtWorksInc/voter-service/tree/artifacts) GitHub repository. The JAR's filename is incremented with each successful build (i.e. `voter-service-0.2.10.jar`).

![Vote Continuous Integration Pipeline](Voter-CI.png)

## Spring Profiles

The service has (3) Spring Profiles, located here, `src/main/resources/application.yml`. They are `default` (`localhost`), `aws-production`, and `docker-production`.

```yaml
spring:
  data:
    mongodb:
      host: localhost
      port: 27017
      database: voters

logging:
  level:
    root: INFO

server:
  port: 8099

info:
    java:
      source: ${java.version}
      target: ${java.version}

---

spring:
  profiles: aws-production
  data:
    mongodb:
      host: 10.0.1.6

logging:
  level:
    root: WARN

---

spring:
  profiles: docker-production
  data:
    mongodb:
      host: mongodb

logging:
  level:
    root: INFO
```

## README

- [Spring Data MongoDB - Reference Documentation](http://docs.spring.io/spring-data/mongodb/docs/current/reference/html/)
- [Accessing MongoDB Data with REST](https://spring.io/guides/gs/accessing-mongodb-data-rest/)
- [Spring Boot Testing](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-testing)
- [Installing Spring Boot applications](https://docs.spring.io/spring-boot/docs/current/reference/html/deployment-install.html#deployment-install)
- [2016 Presidential Candidates](http://www.politics1.com/p2016.htm)
