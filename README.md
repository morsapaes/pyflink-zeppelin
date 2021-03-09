# Interactive Data Exploration with PyFlink and Zeppelin Notebooks

**(WIP)**

See the [slides](https://noti.st/morsapaes/oTSeHt/snakes-on-a-plane-interactive-data-exploration-with-pyflink-and-zeppelin-notebooks) for context.

#### Pre-requisites

* Download [the MUBI dataset](https://www.kaggle.com/clementmsika/mubi-sqlite-database-for-movie-lovers) from Kaggle

#### Getting Zeppelin up and running

You can find instructions on how to run Zeppelin with the Flink interpreter in [this blogpost](https://zjffdu.medium.com/flink-on-zeppelin-part-1-get-started-2591aaa6aa47). In the future, this repo will have a `docker-compose` setup that wraps everything up for you.

## Exploring the Movie Lovers on MUBI dataset (Kaggle)

Once Zeppelin is up and running, import the `ApacheCon@Home 2020_2FH7SDKQ8.zpln` notebook. You'll be looking at three different files:

* `mubi_movie_data.csv` - Data from all movies registered on Mubi.

* `mubi_ratings_data.csv` - Data from ratings on Mubi for users who did not set their profile in private mode (~15 million rows).

* `mubi_ratings_user_data.csv` - Aggregated data from users for a specific day.

### Interpreters

Zeppelin has support for a wide range of [interpreters](http://zeppelin.apache.org/docs/0.9.0/usage/interpreter/overview.html), like Flink, R, Python, Markdown, bash and a lot of others. It allows you to use multiple interpreters in a single notebook, too! Here, you'll use a mix of the `%flink.pyflink` ([PyFlink](https://zeppelin.apache.org/docs/0.9.0/interpreter/flink.html#pyflinkflinkpyflink)) and `%flink.bsql` ([Flink Batch SQL](https://zeppelin.apache.org/docs/0.9.0/interpreter/flink.html#flink-batch-sql)) interpreters. For `%flink.pyflink`, Zeppelin creates the following environment variables for you:

* `s_env`: StreamExecutionEnvironment

* `b_env`: ExecutionEnvironment

* `st_env`: StreamTableEnvironment

* `bt_env`: BatchTableEnvironment

> For simplicity, you'll be batch-reading files stored locally. The cool thing about PyFlink is that you can _e.g._ replace the existing source tables with Kafka-backed ones that continuously consume data and not have to change any of the code (other than `bt_env` to `st_env`).

### Creating the Source Tables

The recommended (and quickest way) to define a source table is to use [SQL DDL](https://ci.apache.org/projects/flink/flink-docs-stable/dev/table/sql/create.html#create-table). Tables are defined as two parts: the logical schema, laying out the table columns and data types; and the connector configuration, defining the physical system "backing" the table. You can use either interpreter for this!

![create_source_tables](https://user-images.githubusercontent.com/23521087/110456128-7edb1180-80c9-11eb-871a-741f870b5940.png)

### Running Basic Analytics

As an example, you can use PyFlink to query the `mubi_movies` table and get the average movie popularity per movie release year. What does it tell us?

:moon: There are two clear outlier years when it comes to popularity (1878 and 1902). If you investigate further, there is only one movie release in each of these years on Mubi ("Sallie Gardner at a Gallop" and "A Trip to the Moon") — they're just really popular!	

:new_moon_with_face: The 1920s were a busy period for silent movie releases, which seem pretty popular with Mubi users.

:sparkles: The 1920s-1960s are considered the golden era of Hollywood, so that can explain the increased popularity of movies released in this period.

![avg_popularity](https://user-images.githubusercontent.com/23521087/110458892-95369c80-80cc-11eb-8464-c8cc6144d594.png)

### Using Dynamic Input Forms

![top10](https://user-images.githubusercontent.com/23521087/110456299-b21da080-80c9-11eb-9e97-d1cfe1ca629d.png)

### Using Pandas (and other Python libraries)

One way to use PyFlink with Pandas is to first use it to reduce the amount of data we want to act upon (which might be a considerably small subset of the original dataset), taking advantage of how performant PyFlink is even on the largest of large datasets; and then convert the resulting table into a Pandas `DataFrame` using [`.toPandas()`](https://ci.apache.org/projects/flink/flink-docs-stable/dev/python/table-api-users-guide/conversion_of_pandas.html#convert-pyflink-table-to-pandas-dataframe).

#### Plotting a Histogram

![histogram](https://user-images.githubusercontent.com/23521087/110456253-9fa36700-80c9-11eb-8f2d-f96b9da35ef9.png)

<hr>

**And that's it!**

If you have any questions or feedback, feel free to DM me on Twitter [@morsapaes](https://twitter.com/morsapaes).