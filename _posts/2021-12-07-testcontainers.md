---
title: "Testcontainers with Spring Boot and Java 11/17"
categories:
  - Posts
tags:
  - testcontainers
  - docker
  - java
  - yaml
  - springboot
  - spring
taxonomy: Testcontainer Java Docker
classes: wide
author_profile: true
---



## What are Testcontainers?

Testcontainers is a JVM library that allows users to run and manage Docker images and control them from Java code.
The integration test additionally runs external components as real Docker containers.

- **Databases** - Run PostgreSQL as a Docker image
- **Mocked HTTP server** - HTTP services by using MockServer or WireMock Docker images
- **Redis** - run real Redis as a Docker image,
- **Message Brokers** - RabbitMQ
- **AWS -** S3, DynamoDB etc
- Any other **application** that can be run as a Docker image

## How to use?

- Setup : Spring Boot and Junit 5
- Dependency to testImplementation "org.testcontainers:postgresql:1.16.2” and testImplementation "org.testcontainers:junit-jupiter:1.16.2”
- And then some wireing to start testcontainers and link them to the test context so that integration tests knows where to look for the containers.

## Example Abstract Class for setup

```java
package com.test;

import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.ApplicationContextInitializer;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.PropertySource;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.support.TestPropertySourceUtils;
import org.springframework.test.web.servlet.request.MockHttpServletRequestBuilder;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.ext.ScriptUtils;
import org.testcontainers.jdbc.JdbcDatabaseDelegate;
import org.testcontainers.junit.jupiter.Testcontainers;
import org.testcontainers.shaded.com.fasterxml.jackson.databind.ObjectMapper;
import org.testcontainers.shaded.com.fasterxml.jackson.databind.SerializationFeature;

import java.util.Optional;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.put;


@Testcontainers
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT, classes = {com.test.Application.class})
@ActiveProfiles(AbstractBaseIntergrationTestConfiguration.ACTIVE_PROFILE_NAME_TEST)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@ContextConfiguration(initializers = AbstractBaseIntergrationTestConfiguration.DockerPostgreDataSourceInitializer.class)
public abstract class AbstractBaseIntergrationTestConfiguration {

    protected static final String JDBC_URL = "jdbc.url=";
    protected static final String JDBC_USERNAME = "jdbc.username=";
    protected static final String JDBC_PASSWORD = "jdbc.password=";
    protected static final String JDBC_DRIVER_CLASS_NAME_ORG_POSTGRESQL_DRIVER = "jdbc.driverClassName=org.postgresql.Driver";
    protected static final String ACTIVE_PROFILE_NAME_TEST = "TestContainerTests";

    //--
    public static PostgreSQLContainer<?> postgreDBContainer;
    protected ObjectMapper objectMapper = new ObjectMapper().disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);

    static {
        // Init DB Script here
        postgreDBContainer = new PostgreSQLContainer<>(IntegrationTestConstants.POSTGRESQL_IMAGE);
        postgreDBContainer
                .withInitScript(IntegrationTestConstants.INIT_DB_SCRIPT)
                .withDatabaseName(IntegrationTestConstants.DB_NAME)
                .withUsername(IntegrationTestConstants.DB_USERNAME)
                .withPassword(IntegrationTestConstants.DB_PASSWORD);

        postgreDBContainer.start();
        var containerDelegate = new JdbcDatabaseDelegate(postgreDBContainer, "");

        // Adding Database scripts here
        ScriptUtils.runInitScript(containerDelegate, IntegrationTestConstants.MISSING_TABLES_SQL);
        ScriptUtils.runInitScript(containerDelegate, IntegrationTestConstants.SAMPLE_DATA_SQL);
    }

    // This class adds the DB properties to Testcontainers.
    public static class DockerPostgreDataSourceInitializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {

        @Override
        public void initialize(ConfigurableApplicationContext applicationContext) {

            TestPropertySourceUtils.addInlinedPropertiesToEnvironment(
                    applicationContext,
                    JDBC_DRIVER_CLASS_NAME_ORG_POSTGRESQL_DRIVER,
                    JDBC_URL + postgreDBContainer.getJdbcUrl(),
                    JDBC_USERNAME + postgreDBContainer.getUsername(),
                    JDBC_PASSWORD + postgreDBContainer.getPassword()
            );
        }
    }
}
```

## How to write a test?

```java
@Test
void checkIfUserExistInIdealCase() throws Exception {

  request.put("email", "abc@test.com");
  
  final MockHttpServletRequestBuilder postObject = getPostRequestExecutorBuilder("http://localhost:8080/v1/checkemail/", Optional.empty());
  final MvcResult result = mockMvc.perform(postObject.content(request.toString())).andExpect(status().isOk()).andReturn();
  final String content = result.getResponse().getContentAsString();
  
  final SyncResponseDto responseDto = objectMapper.readValue(content, SyncResponseDto.class);
  
  assertThat(responseDto.getResponseReturnCode()).isEqualTo(ResponseReturnCode.USER\_EXIST);
}
```

## Advantages

- Run Integration Tests offline.
- You run tests against real components, PostgreSQL database instead of the H2 database.
- You can mock AWS services.
- Implementation and tests can be written by developers same time when raising a PR.
- Multiple containers can be added and it’s consistent across all developer machines. Same versions etc and runs without any efforts with GitHub actions.
## Disadvantages

- The main limitation is, that containers cannot be reused between test classes.
- Adding “one more” external dependency.
- Takes a bit more time than usual to start a container, 4 - 5 seconds for Postgres VS 0.5 seconds for H2.
- When running locally, local machine should be powerful enough too ;)
- More RAM, More Power as multiple containers can be run.

### References

* [Testcontainer](https://www.testcontainers.org/)
* [Test-Container Java](https://github.com/testcontainers/testcontainers-java)
* [Testcontainer Example](https://www.baeldung.com/spring-boot-testcontainers-integration-test)
