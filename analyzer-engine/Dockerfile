# Use Maven for building the project
FROM maven:3.8.6-eclipse-temurin-17 AS build
WORKDIR /app

# Copy project files
COPY pom.xml .
COPY src ./src

# Copy local dependencies (including core-analyzer-lib)
COPY libs /app/libs

# Manually install core-analyzer-lib
RUN mvn install:install-file \
    -Dfile=/app/libs/core-analyzer-lib-1.0-SNAPSHOT.jar \
    -DgroupId=com.lca \
    -DartifactId=core-analyzer-lib \
    -Dversion=1.0-SNAPSHOT \
    -Dpackaging=jar \
    -DgeneratePom=true

# Ensure dependencies are available before building
RUN mvn dependency:go-offline

# Build the application
RUN mvn package -DskipTests

# Use a lightweight JDK image to run the built app
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app

# Copy the built JAR file from the build stage
COPY --from=build /app/target/analyzer-lca-spring-0.0.1-SNAPSHOT.jar app.jar

# Expose the application port
EXPOSE 8080

# Run the Spring Boot application
CMD ["java", "-jar", "app.jar"]
