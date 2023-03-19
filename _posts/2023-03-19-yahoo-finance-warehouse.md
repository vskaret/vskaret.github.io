Once upon a time I did a side project where I visualized some financial data from yahoo finance after importing it to my own database using airbyte and then modeling the data how I'd like it using dbt.

I wrote a blog post about it on my website a while ago, but I think I didn't manage to migrate the backend correctly from heroku to fly.io and now the data is gone gone.

Luckily I stored it on my computer so I will just copy and paste it below!

Guide for setting up and using a git repository for getting yahoo finance data!

## Introduction

As a part of a hobby project with a friend where we need stonk data, we've created a [git repository](https://github.com/Travbula/yahoo-finance-warehouse) for fetching, storing and visualizing data from yahoo finance with some interesting tools.

One of the purposes of this project is  to set up one's own stock portfolio monitoring tool where much of the day-to-day noise of the market and happenings in the world is removed.

To get everything in this project up and running is quite a bit of hassle, so this post attempts to guide you through setting everything up from scratch. I will assume you are using a fresh install of [Ubuntu](https://ubuntu.com/) and have some knowledge of the terminal ([help](https://www.youtube.com/watch?v=s3ii48qYBxA)).

Most of the information here is available in the repository's readme, however this guide will be more visual including some screenshots. If you have any issues getting things to work, please post them to the [issues section](https://github.com/Travbula/yahoo-finance-warehouse/issues) of the repository. If the issue is specific to one of the tools, other options are [dbt slack](https://www.getdbt.com/community/join-the-community/), [airbyte slack](https://airbytehq.slack.com/join/shared_invite/zt-1bx4v2t4m-vhZaQu1OdJTyk1M1nSxjBA) and [lightdash slack](https://lightdash-community.slack.com/join/shared_invite/zt-1bfmfnyfq-nSeTVj0cT7i2ekAHYbBVdQ#/shared-invite/email).

This post includes limited information about the concepts in Airbyte, to learn more about those check out the [Airbyte docs](https://airbytehq.github.io/cloud/core-concepts).

**Table of contents**

* Prerequisites
* Setup of warehouse database
* Fetch data with Airbyte
* Model the data inside of the warehouse with dbt
* Visualize data with Lightdash

## Prerequisites

If docker stuff is not working, see [this readme](https://github.com/Travbula/div-setup-help/blob/master/ubuntu-setup.md#docker) for help with installing a new version of docker.

Clone the git repo

```sh
git clone https://github.com/Travbula/yahoo-finance-warehouse.git
```

## Setup of warehouse database

Open the `database` folder inside of the repo:

```sh
cd yahoo-finance-warehouse/database
```
Start the database

```sh
docker-compose up -d
```

Connect to the database with some tool, e.g. adminer at [localhost:8090](http://localhost:8090/?pgsql=warehouse&username=postgres&db=postgres) and login with the default information:

* Server: warehouse
* Username: postgres
* Password: postgres
* Database: postgres

![1_adminer1.png](https://res.cloudinary.com/travbula/image/upload/v1668021806/1_adminer1_33722e5d54.png)

Then open the sql command panel.

![2_adminer_index.png](https://res.cloudinary.com/travbula/image/upload/v1668022714/2_adminer_index_4890a807d3.png)

In the sql command panel, run the sql commands inside of the `create_db.sql` file in the `database` folder in the repo.

![3_adminer_sql_command.png](https://res.cloudinary.com/travbula/image/upload/v1668022723/3_adminer_sql_command_143d76df65.png)

After running this, you should see a bunch of green text signifying the commands were successful.

![4_adminer_sql_success.png](https://res.cloudinary.com/travbula/image/upload/v1668022734/4_adminer_sql_success_1b0f24130b.png)

Now the database if ready! Let's get to getting data into our database with Airbyte.

## Fetch data with Airbyte

Enter the airbyte folder folder and run

```sh
docker-compose up
```

Then open localhost:8000 in the web browser. It takes a little while before Airbyte is ready and running. After a while you should see something like the following.

![5_airbyte_preferences.png](https://res.cloudinary.com/travbula/image/upload/v1668023447/5_airbyte_preferences_f2953f65b7.png)

### Add source connectors

After filling out and clicking continue, it is time to add some source connectors that parses the yahoo finance API. First click settings in the bottom left, then click Sources and then + New connector.

![6_airbyte_source_page.png](https://res.cloudinary.com/travbula/image/upload/v1668023908/6_airbyte_source_page_f1d3d20bca.png)

Fill in the following into the add new connector window.

* Connector display name: Yahoo Finance Financials
* Docker repository name: travbula/source-yahoo-finance-financials
* Docker image tag: 0.1.32
* Connector Documentation URL: https://travbula.no

![7_airbyte_connector_financials.png](https://res.cloudinary.com/travbula/image/upload/v1668026418/7_airbyte_connector_financials_acbecb34e4.png)

Then click and Add. Click + New connector again to add the Yahoo Finance Price source.

![8_airbyte_connector_price.png](https://res.cloudinary.com/travbula/image/upload/v1668026419/8_airbyte_connector_price_f203103bb2.png)

Now we have added the two source connectors we will utilize to get data from yahoo finance. The next step is to configure our destination, which is where we will store the data.

### Add warehouse destination

In this section we add the destination for the data. This is a simple step, as we utilize a destination component already incorporated into Airbyte.

First, click Destinations.

![10_airbyte_add_destination.png](https://res.cloudinary.com/travbula/image/upload/v1668026433/10_airbyte_add_destination_29e5d06418.png)

Then click New distination and choose Postgres.

![11_airbyte_postgres_destination_setup.png](https://res.cloudinary.com/travbula/image/upload/v1668026434/11_airbyte_postgres_destination_setup_882e6171a1.png)

After picking Postgres, fill in the info as above. You could also use the airbyte_user with the 'super_duper_secret' password.

Next up we add our sources.

### Add sources

A source is where we get our data from. We will set up one source for financial data and one source for stock price data.

First, click sources and look for Yahoo Finance Financials.

![9_airbyte_add_source_screen.png](https://res.cloudinary.com/travbula/image/upload/v1668026419/9_airbyte_add_source_screen_6021401b97.png)

Fill in any tickers you'd like into the tickers box. Note that you might have to tab out of the box for each ticker to let Airbyte know they are not the same ticker. Also, the tickers must exist on [finance.yahoo.com](https://finance.yahoo.com). Sometimes the naming on yahoo finance is different from your stock exchange's ticker naming (as in SDSD.OL instead of SDSD).

![12_airbyte_finance_source.png](https://res.cloudinary.com/travbula/image/upload/v1668026434/12_airbyte_finance_source_31643cf731.png)

Click set up source. Next we will connect the source to our destination. Simply click add destination and then Warehouse.

![13_airbyte_connect_source_to_destination.png](https://res.cloudinary.com/travbula/image/upload/v1668026509/13_airbyte_connect_source_to_destination_49d2913c05.png)

Now you will get a configuration screen for the connection. The most important part is to turn off "Normalized tabular data" at the bottom as this will make the sync fail. I also suggest setting replication frequency to manual.

![14_airbyte_connection_setup.png](https://res.cloudinary.com/travbula/image/upload/v1668026510/14_airbyte_connection_setup_07b3cbdb55.png)

Click set up connection and then sync now in the next window.

![15_sync_connection.png](https://res.cloudinary.com/travbula/image/upload/v1668026636/15_sync_connection_726a0a2156.png)

The sync should start running.

![16_airbyte_sync_running.png](https://res.cloudinary.com/travbula/image/upload/v1668026649/16_airbyte_sync_running_83fbabea77.png)

If you get sync succeeded, you should now have successfully transferred the data from the yahoo finance api into your database.

![17_sync_success.png](https://res.cloudinary.com/travbula/image/upload/v1668026661/17_sync_success_9218ab02e5.png)

The next step is to follow the same process for a source for the stock prices. Use the Yahoo Finance Price source connector for this. The image below contains a suggestion for the source configuration.

![18_airbyte_finance_price.png](https://res.cloudinary.com/travbula/image/upload/v1668026680/18_airbyte_finance_price_006799c489.png)

After setting up and syncing the price data, we are now ready to make the raw data analyzable with models made in dbt.

## Model the data inside of the warehouse with dbt

In this section we will take the raw data from above and make them ready for visualization in a BI tool like Lightdash.

First up is to set up dbt.

#### Setup dbt

Open the dbt-model folder.

Create a python virtual environment.

```sh
python3 -m venv .venv
```

Activate environment.

```sh
source .venv/bin/activate
```

Install packages.

```sh
python -m pip install 
```

Check your connection.

```sh
dbt debug --profiles-dir=.
```

Tips: if you'd like to skip `--profiles-dir=.` each time, copy the `profiles.yml` file into `~/dbt/`.

Install dbt packages.

```sh
dbt deps
```

#### Run dbt

```sh
dbt run
```

If this was successful, your datawarehouse should have a bunch of views in the `analytics` schema. The most interesting views are those in the `dbt-model/models/marts/` folder:

* `dim_company`
* `dim_currency`
* `fact_balance_statement`
* `fact_free_cashflow`
* `fact_income_statement`
* `fact_stock_price`
* `latest_market_capitalization`
* `latest_stock_price_in_year`
* `market_capitalization`

See more information about these in the documentation generated in the next step.

#### Browse the documentation

Generate documentation of the dbt stuff

```sh
dbt docs generate
```

Start a web server that hosts the documentation

```sh
dbt docs serve --port 9000
```

Open the documentation in your web browser at localhost:9000.

#### dbt development with vscode

This is a bit unrelated to everything else, but there are two nice dbt extensions in VSCode called "dbt Power User" and "dbt formatter". 

## Visualize data with Lightdash

Enter lightdash folder and run

```sh
docker-compose --env-file ./.env.fast-install -f docker-compose.yml up --detach --remove-orphans || true
```

Open [http://localhost:8080](http://localhost:8080).

Then follow the images below (todo: write descriptions).

![19_lightdash_login_screen.png](https://res.cloudinary.com/travbula/image/upload/v1668112049/19_lightdash_login_screen_490ac47328.png)

Create an account.

![20_lightdash_role.png](https://res.cloudinary.com/travbula/image/upload/v1668112049/20_lightdash_role_bb83ea1701.png)

The organization name becomes the name of the whole Lightdash project or something like that. Not so important.

![21_lightdash_setup_start.png](https://res.cloudinary.com/travbula/image/upload/v1668112049/21_lightdash_setup_start_38b442c8ce.png)

Choose PostgreSQL warehouse.

![22_choose_manual.png](https://res.cloudinary.com/travbula/image/upload/v1668112049/22_choose_manual_91e12d1e61.png)

Click create project manually. (Feel free to use the Lightdash install tool if you'd like to instead.)

![23_ive_defined_em.png](https://res.cloudinary.com/travbula/image/upload/v1668112049/23_ive_defined_em_097b43f0e4.png)

We have already defined some metrics, so click I've defined them.

The next two images contains the connection info to the database and the info of the dbt project.

![24_lightdash_connection.png](https://res.cloudinary.com/travbula/image/upload/v1668112049/24_lightdash_connection_8c135845d5.png)

![25_lightdash_dbt_connection.png](https://res.cloudinary.com/travbula/image/upload/v1668112359/25_lightdash_dbt_connection_004c97cc85.png)

![26_lightdash_success.png](https://res.cloudinary.com/travbula/image/upload/v1668112360/26_lightdash_success_7253daf7b4.png)

Hopefully you get the successful connection message after clicking test & compile project.

Choose show entire project and click save changes.

Then in the top left click explore and then tables.

![27_lightdash_explore.png](https://res.cloudinary.com/travbula/image/upload/v1668112359/27_lightdash_explore_f33db8db96.png)

In the left menu of the screen you will see all of the available tables. Click Market capitalization.

Now you will see all of the available dimensions in the Market capitalization table and all of the metrics.

![28_lightdash_query.png](https://res.cloudinary.com/travbula/image/upload/v1668112484/28_lightdash_query_8c9e81750c.png)

Choose the dimensions date and ticker, and the metrics Market capitalization (avg) and Net income (avg) by clicking on them in the left menu. Note that the avg naming is in this case a bit weird as we will set up our visualization such that they are an average of one data point.

After choosing the dimensions and metrics, click Run query.

![29_lightdash_results.png](https://res.cloudinary.com/travbula/image/upload/v1668112563/29_lightdash_results_c37c716db5.png)

Now you should see some stuff under Charts. For me, the x-axis was sorted by market cap instead of date. To fix this, add sorting by date by clicking the three dots to the right of the Date label and click Sort A-Z.

![30_lightdash_sort_az.png](https://res.cloudinary.com/travbula/image/upload/v1668112614/30_lightdash_sort_az_9b1d37c647.png)

Then remove sorting by market capi by clicking the Sorted by 2 fields thing and clicking the x to the right of sort by market cap.

![31_remove_order_by.png](https://res.cloudinary.com/travbula/image/upload/v1668112641/31_remove_order_by_9173e45dfb.png)

Now the chart should make a bit more sense!

![32_lightdash_new_results.png](https://res.cloudinary.com/travbula/image/upload/v1668112696/32_lightdash_new_results_f55a3d2c06.png)

If you'd like to, you can play around with how the data is visualized, e.g. by having the market cap visualized by a line.

![33_lightdash_configure.png](https://res.cloudinary.com/travbula/image/upload/v1668112718/33_lightdash_configure_e91c6ac3d0.png)

![34_lightdash_graph.png](https://res.cloudinary.com/travbula/image/upload/v1668112776/34_lightdash_graph_995d786249.png)

You can save the chart, which lets you add it to a dashboard later.

![35_lightdash_savechart.png](https://res.cloudinary.com/travbula/image/upload/v1668113231/35_lightdash_savechart_005a13f64d.png)

If you've come this far, congratulations! You have set up your own data warehouse with data from yahoo finance. Further, you used the date inside of your warehouse to visualize something meaningful in a BI tool.
