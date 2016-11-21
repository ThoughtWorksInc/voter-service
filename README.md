[![Build Status](https://travis-ci.org/ThoughtWorksInc/voter-service.svg?branch=master)](https://travis-ci.org/ThoughtWorksInc/voter-service)

# Voter Service

## Introduction

A sample Spring Boot RESTful API microservice, backed by MongoDB. The Voter service exposes several HTTP endpoints, listed below. Calling those endpoints, end-users can review candidates, submit a vote, view voting results, and view the winner. End-users can also create random voting data for simulations.

## Quick Start

The Voter service requires MongoDB to be pre-installed and running locally, on port `27017`. To clone, build, test, and run the Voter service, locally:

```bash
git clone https://github.com/ThoughtWorksInc/voter-service.git
cd voter-service
./gradlew clean cleanTest build
java -jar build/libs/voter-service-0.1.0.jar
```

## Primary Service Endpoints

Out of the box, the service runs on `localhost`, port `8099`. By default, the service looks for MongoDB on `localhost`, port `27017`.

- Create Random Sample Data (GET): <http://localhost:8099/simulation>
- List Candidates (GET): <http://localhost:8099/candidates>
- Submit Vote (POST): <http://localhost:8099/votes>
- View Voting Results (GET): <http://localhost:8099/results>
- View Winner (GET): <http://localhost:8099/winner>
- Service Health (GET): <http://localhost:8099/health>
- Service Metrics (GET): <http://localhost:8099/metrics>
- Other [Spring Actuator](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready) endpoints include: `/mappings`, `/env`, `/configprops`, etc.
- Other [HATEOAS](https://spring.io/guides/gs/rest-hateoas) endpoints for `/votes` include: DELETE, PATCH, PUT, page sort, size, etc.

## How to POST a Vote:

HTTPie

```text
http POST http://localhost:8099/votes vote="Hillary Clinton"
```

cURL

```text
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{ "vote": "Hillary Clinton" }' \
  "http://localhost:8099/votes"
```

wget

```text
wget --method POST \
  --header 'content-type: application/json' \
  --body-data '{ "vote": "Hillary Clinton" }' \
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

`http http://localhost:8099/results`

```json
{
    "results": [
        {
            "count": 3,
            "vote": "Chris Keniston"
        },
        {
            "count": 2,
            "vote": "Darrell Castle"
        },
        {
            "count": 8,
            "vote": "Donald Trump"
        },
        {
            "count": 4,
            "vote": "Gary Johnson"
        },
        {
            "count": 10,
            "vote": "Hillary Clinton"
        },
        {
            "count": 5,
            "vote": "Jill Stein"
        }
    ]
}
```

`http http://localhost:8099/winner`

```json
{
    "count": 10,
    "vote": "Hillary Clinton"
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

## Build Artifact

The project's source code is continuously built and tested on every code check-in to GitHub. If all unit tests pass, the resulting Spring Boot JAR is stored in the `artifacts` branch of the  [ThoughtWorksInc/voter-service](https://github.com/ThoughtWorksInc/voter-service/tree/artifacts) GitHub repository. The JAR file's name is incremented with each successful build.

![Vote Continuous Integration Pipeline](Voter-CI.png)

## Spring Profiles

The service has three Spring Profiles, located here: `src/main/resources/application.yml`. They are `default` (`localhost`), `aws-production`, and `docker-production`.

## README

- [Accessing MongoDB Data with REST](https://spring.io/guides/gs/accessing-mongodb-data-rest/)
- [Spring Boot Testing](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-testing)
- [Installing Spring Boot applications](https://docs.spring.io/spring-boot/docs/current/reference/html/deployment-install.html#deployment-install)
