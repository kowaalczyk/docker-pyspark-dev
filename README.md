# docker-pyspark-dev

Dockerfile for developing python applications and libraries for Apache Spark:

- includes latest [Apache Spark](https://spark.apache.org/) and [Apache Hadoop](https://hadoop.apache.org/) libraries
- uses [poetry](https://python-poetry.org/) for modern python development and packaging


## Example usage

Create a new poetry project:
```shell
poetry new my-app
cd my-app
```

Add pytest and pyspark to your dependencies in `pyproject.toml`:
```toml
[tool.poetry.dependencies]
python = "^3.7"
pyspark = "^2.4.5"

[tool.poetry.dev-dependencies]
pytest = "^5.2"
pytest-spark = "^0.6.0"
```

Add a test that checks if pyspark is working correctly in `tests/test_my_app.py`:
```python
def test_spark(spark_context):
    # spark_context is a pytest fixture provided by pytest-spark plugin
    assert spark_context.parallelize(range(3)).collect() == [0, 1, 2]
```

Create a `Dockerfile` in your project:
```Dockerfile
FROM kowaalczyk/pyspark-dev:latest

WORKDIR /usr/src/app

# copy poetry settings and install dependencies (so that they will be cached)
ADD pyproject.toml poetry.lock ./
RUN poetry install --no-interaction --no-ansi

# add project source code and tests
ADD .flake8 mypy.ini ./
ADD tests ./tests
ADD app ./app
```

Build the image:
```shell
docker build -t my-app .
```

Run poetry commands in the docker container:
```shell
docker run my-app:latest poetry run pytest
```

This example is based on `kowaalczyk/spark-minimal-algorithms` python package.


## Details

- Apache Spark version: 2.4.5 (latest stable version)
- Hadoop version: 2.7 (latest stable version)
- Python version: 3.7.3 (Spark 2.4.5 is not compatible with Python 3.8)


## Legal note

This project is not associated or maintained by Apache Software Foundation.

Apache Hadoop, Hadoop, Apache, the Apache feather logo, and the Apache Hadoop project logo are either registered trademarks or trademarks of the Apache Software Foundation in the United States and other countries.

Apache Spark, Spark, Apache, the Apache feather logo, and the Apache Spark project logo are either registered trademarks or trademarks of The Apache Software Foundation in the United States and other countries.
