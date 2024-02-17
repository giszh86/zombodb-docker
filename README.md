# ZomboDB Docker

ZomboDB is a Postgres extension that enables efficient full-text searching via the use of indexes backed by Elasticsearch.

This repo build a docker image which integrate ZomboDB extension with Postgres.

## Reference informations

[ZomboDB github](https://github.com/zombodb/zombodb/tree/master)

## Build docker image

Sample command:
```shell
docker build --build-arg="PG_VER=15" -t postgres-zombodb:15 .
```

The build argument `PG_VER` is optional, `12`, `13`, `14`, `15` are available, default value is `15`.

## Usage

* Create extension

  You should first create zombodb extension into your database.
  Sample SQL:
  ```sql
  CREATE EXTENSION zombodb;
  ```

* Create table with index

  Create table sample SQL:
  ```sql
  CREATE TABLE sample_table (
    id serial,
    content zdb.fulltext NOT NULL, -- column with `zdb.fulltext` type
    timstamp timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
  );
  ```

  Create index sample SQL:
  ```sql
  CREATE INDEX idx_sample_table
    ON sample_table
    USING zombodb ((sample_table.*))
    WITH (url='http://elasticsearch:9200/')
  ```

  *Notice that if your Elasticsearch server required user auth, you can pass username/password in URL format like `http://username:password@elasticsearch:9200/`*

* Query data with full-text search

  Sample SQL:
  ```sql
  SELECT * FROM sample_table WHERE sample_table ==> 'some keywords';
  ```