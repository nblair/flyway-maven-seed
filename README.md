## flyway-maven-seed

[![Build Status](https://travis-ci.org/nblair/flyway-maven-seed.svg?branch=master)](https://travis-ci.org/nblair/flyway-maven-seed)

This project is intended to serve as an example on how to manage a set of DDL or other database statements with a tool called Flyway: http://flywaydb.org/

### Requirements

1. Java (required for Maven)
2. Maven 3.2+ (see: http://maven.apache.org/download.cgi#Installation)

### Adding database migration files

Database migration files (DDL statements, insert/update/delete statements, etc) must be added under the folder *src/main/resources/database/migrations*. Each migration file should have a name that matches the following:

    V1__Few_word_phrase_describing_function.sql
    
The capital V at the beginning is required, and the number immediately preceding represents the order in which this statement should be executed. After the number, there are 2 underscores ('_'), followed by the rest of the file name. An underscore separated phrase is appropriate (and will be stored in the description column of Flyway's metadata table with whitespace characters replacing the underscores).

This syntax also supports a dot form for the order number. Say for example you have 2 migrations already deployed:

    V7__does_something.sql
    V8__does_something_after_7.sql
    
You realize later on that something needs to run in between the 2 statements. Simply name the new file with *7.1*, like so:

    V7__does_something.sql
    V7.1__does_something_after_7_but_before_8.sql
    V7.2__does_something_after_7_1_but_before_8.sql
    V8__does_something_after_7.sql
    
Each migration file is run in a separate transaction.

### Running

This project is configured by default to execute the migrations against a local [Hypersql](http://hsqldb.org/) database (files are stored in target/hsqldb/).

1. *If you need to run the database migrations against a different database*, copy flyway-SAMPLE.properties as flyway.properties and edit the driver and credentials to match your environment. Otherwise proceed to step 2.
2. Execute the following command:

> mvn compile flyway:migrate

The compile goal is required. The plugin is configured to look at the classpath location *database/migrations*, Maven's process-resources phase will result in the files moving from src/main/resources/database/migrations to the correct location.

### Additional commands

> mvn flyway:validate

This command checks to see if there are migrations in the source directory that have not yet been migrated, which if true results in a build failure. Alternatively, if all migrations have been run, the build is successful.

> mvn flyway:info

This command prints to the console which migrations have been performed, when, and whether they succeeded or not.

> mvn flyway:clean

This command drops the configured schema(s). The plugin configuration in the pom specifies "example_schema" as the name of the schema; you can override this property in your flyway.properties file.
