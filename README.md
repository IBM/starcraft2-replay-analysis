*Read this in other languages: [中国](README-cn.md).*
# StarCraft II Replay Analysis with Jupyter Notebooks

*Read this in other languages: [한국어](README-ko.md), [中国](README-cn.md).*

In this developer journey we will use Jupyter notebooks to analyze
StarCraft II replays and extract interesting insights.

When the reader has completed this journey, they will understand how to:

* Create and run a Jupyter notebook in DSX.
* Use DSX Object Storage to access a replay file.
* Use sc2reader to load a replay into a Python object.
* Examine some of the basic replay information in the result.
* Parse the contest details into a usable object.
* Visualize the contest with Bokeh graphics.
* Store the processed replay in Cloudant.

The intended audience for this journey is application developers who
need to process StarCraft II replay files and build powerful visualizations.

![](doc/source/images/architecture.png)

## Included components

* [IBM Data Science Experience](https://www.ibm.com/bs-en/marketplace/data-science-experience): Analyze data using RStudio, Jupyter, and Python in a configured, collaborative environment that includes IBM value-adds, such as managed Spark.

* [Cloudant NoSQL DB](https://console.ng.bluemix.net/catalog/services/cloudant-nosql-db/?cm_sp=dw-bluemix-_-code-_-devcenter): Cloudant NoSQL DB is a fully managed data layer designed for modern web and mobile applications that leverages a flexible JSON schema.

* [Bluemix Object Storage](https://console.ng.bluemix.net/catalog/services/object-storage/?cm_sp=dw-bluemix-_-code-_-devcenter): A Bluemix service that provides an unstructured cloud data store to build and deliver cost effective apps and services with high reliability and fast speed to market.

## Featured technologies

* [Jupyter Notebooks](http://jupyter.org/): An open-source web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text.

* [sc2reader](http://sc2reader.readthedocs.io/en/latest/): A Python library that extracts data from various [Starcraft II](http://us.battle.net/sc2/en/) resources to power tools and services for the SC2 community.

* [pandas](http://pandas.pydata.org/): A Python library providing high-performance, easy-to-use data structures.

* [Bokeh](http://bokeh.pydata.org/en/latest/): A Python interactive visualization library.

# Watch the Video

[![](http://img.youtube.com/vi/iKToQpJZIL0/0.jpg)](https://www.youtube.com/watch?v=iKToQpJZIL0)

# Steps

Follow these steps to setup and run this developer journey. The steps are
described in detail below.

1. [Sign up for the Data Science Experience](#1-sign-up-for-the-data-science-experience)
1. [Create Bluemix services](#2-create-bluemix-services)
1. [Create the notebook](#3-create-the-notebook)
1. [Add the replay file](#4-add-the-replay-file)
1. [Create a connection to Cloudant](#5-create-a-connection-to-cloudant)
1. [Run the notebook](#6-run-the-notebook)
1. [Analyze the results](#7-analyze-the-results)
1. [Save and share](#8-save-and-share)

## 1. Sign up for the Data Science Experience

Sign up for IBM's [Data Science Experience](http://datascience.ibm.com/). By signing up for the Data Science Experience, two services: ``DSX-Spark`` and ``DSX-ObjectStore`` will be created in your Bluemix account.

## 2. Create Bluemix services

Create the following Bluemix service by clicking the **Deploy to Bluemix**
button or by following the links to use the Bluemix UI and create it.

  * [**Cloudant NoSQL DB**](https://console.ng.bluemix.net/catalog/services/cloudant-nosql-db)

[![Deploy to Bluemix](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/ibm/starcraft2-replay-analysis)

## 3. Create the notebook

Use the menu on the left to select `My Projects` and then `Default Project`.
Click on `Add notebooks` (upper right) to create a notebook.

* Select the `From URL` tab.
* Enter a name for the notebook.
* Optionally, enter a description for the notebook.
* Enter this Notebook URL: https://github.com/IBM/starcraft2-replay-analysis/blob/master/notebooks/starcraft2_replay_analysis.ipynb
* Click the `Create Notebook` button.

![](doc/source/images/create_notebook_from_url.png)

## 4. Add the replay file

#### Add the replay to the notebook
Use `Find and Add Data` (look for the `10/01` icon)
and its `Files` tab. From there you can click
`browse` and add a .SC2Replay file from your computer.

> Note:  If you don't have your own replays, you can get our example by cloning
this git repo. Look in the `data/example_input` directory.

![](doc/source/images/add_file.png)

#### Create an empty cell
Use the `+` button above to create an empty cell to hold
the inserted code and credentials. You can put this cell
at the top or anywhere before `Load the replay`.

#### Insert to code
After you add the file, use its `Insert to code` drop-down menu.
Make sure your active cell is the empty one created earlier.
Select `Insert StringIO object` from the drop-down menu.

![](doc/source/images/insert_to_code.png)

Note: This cell is marked as a hidden_cell because it contains
sensitive credentials.

#### Fix the code!

We don't want to treat the replay as unicode text. We want the bytes.
In the inserted code, change this import:
```python
from io import StringIO
```
To use StringIO like this:
```python
from StringIO import StringIO
```

Change this return line:
```python
return StringIO(resp2.text)
```
To use the "content" bytes like this:
```python
return StringIO(resp2.content)
```

![](doc/source/images/inserted_stringio.png)

#### Fix-up variable names
The inserted code includes a generated method with credentials and then calls
the generated method to set a variable with a name like `data_1`. If you do
additional inserts, the method can be re-used and the variable will change
(e.g. `data_2`).

Later in the notebook, we set `replay_file = data_1`. So you might need to
fix the variable name `data_1` to match your inserted code.

## 5. Create a connection to Cloudant

#### Create a database
Before you an add a connection, you need a database.
Use your Bluemix dashboard to find the service you created.
If you used `Deploy to Bluemix` look for `sc2-cloudantNoSQLDB-service`.
If you created the service directly in Bluemix you may have picked a
different name or used the default name of `Cloudant NoSQL DB-` with a random
suffix.

* Click on the service.

* Use the `Manage` tab and hit the `LAUNCH` button.

* Click on the Databases icon on the left menu.

* Click `Create Database` on the top. When prompted for a database
name, you can use any name. We just need any database before creating
a connection.

#### Add a new connection to the project
Use the DSX menu to select the project containing the notebook.

Use `Find and Add Data` (look for the `10/01` icon)
and its `Connections` tab. From there you can click `Create Connection`.

![](doc/source/images/create_connection.png)

Give the connection a name and optionally a description.
Under `Service Category` select the `Data Service` button.
Use the `Target service instance` drop-down and select your Cloudant NoSQL DB instance
(e.g., `sc2-cloudantNoSQLDB-service`).

![](doc/source/images/add_cloudant_conn.png)

Make sure the connection you created is enabled with a checkbox in `Connections`.

#### Create an empty cell
Use the `+` button above to create an empty cell to hold
the inserted code and credentials. You can put this cell
at the top or anywhere before `Storing replay files`.

#### Add the Cloudant credentials to the notebook
Use `Find and Add Data` (look for the `10/01` icon)
and its `Connections` tab. You should see the
connection name created earlier.
Make sure your active cell is the empty one created earlier.
Select `Insert to code` (below your connection name).

![](doc/source/images/insert_cloudant_conn.png)

Note: This cell is marked as a `hidden_cell` because it contains sensitive credentials.

#### Fix-up variable names
The inserted code includes a dictionary with credentials assigned to a variable
with a name like `credentials_1`. It may have a different name (e.g. `credentials_2`).
Rename it or reassign it if needed. The notebook code assumes it will be `credentials_1`.

## 6. Run the notebook

When a notebook is executed, what is actually happening is that each code cell in
the notebook is executed, in order, from top to bottom.

Each code cell is selectable and is preceded by a tag in the left margin. The tag
format is `In [x]:`. Depending on the state of the notebook, the `x` can be:

* A blank, this indicates that the cell has never been executed.
* A number, this number represents the relative order this code step was executed.
* A `*`, this indicates that the cell is currently executing.

There are several ways to execute the code cells in your notebook:

* One cell at a time.
  * Select the cell, and then press the `Play` button in the toolbar.
* Batch mode, in sequential order.
  * From the `Cell` menu bar, there are several options available. For example, you
    can `Run All` cells in your notebook, or you can `Run All Below`, that will
    start executing from the first cell under the currently selected cell, and then
    continue executing all cells that follow.
* At a scheduled time.
  * Press the `Schedule` button located in the top right section of your notebook
    panel. Here you can schedule your notebook to be executed once at some future
    time, or repeatedly at your specified interval.

## 7. Analyze the results

The result of running the notebook is a report which may be shared with or
without sharing the code. You can share the code for an audience that wants
to see how you came your conclusions. The text, code and output/charts are
combined in a single web page. For an audience that does not want to see the
code, you can share a web page that only shows text and output/charts.

### Basic output

Basic replay information is printed out to show you how you can start working
with a loaded replay. The output is also, of course, very helpful to identify
which replay you are looking at.

![](doc/source/images/basic_info.png)

### Data preparation

If you look through the code, you'll see that a lot of work went into preparing
the data.

#### Unit and building groups

List of strings were created for the _known_ units and groups. These are needed
to recognize the event types.

#### Event handlers

Handler methods were written to process the different types of events and
accumulate the information in the player's event list.

#### The ReplayData class

We created the `ReplayData` class to take a replay stream of bytes and process
them with all our event handlers. The resulting player event lists are stored
in a `ReplayData` object. The `ReplayData` class also has an `as_dict()`
method. This method returns a Python dictionary that makes it easy to process
the replay events with our Python code. We also use this dict to create a
Cloudant JSON document.

### Visualization

To visualize the replay we chose to use 2 different types of charts and
show a side-by-side comparison of the competing players.

* Nelson rules charts
* Box plot charts

We generate these charts for each of the following metrics. You will get a
good idea of how the players are performing by comparing the trends for these
metrics.

* Mineral collection rate
* Vespene collection rate
* Active workers count
* Supply utilization (used / available)
* Worker/supply ratio (workers / supply used)

#### Box plot charts

Once you get to this point, you can see that generating a box plot is quite
easy thanks to _pandas DataFrames_ and _Bokeh BoxPlot_.

The box plot is a graphical representation of the summary statistics for the
metric for each player. The "box" covers the range from the first to the third
quartile. The horizontal line in the box shows the mean. The "whisker" shows
the spread of data outside these quartiles. Outliers, if any, show up as
markers outside the whisker lines.

For each metric, we show the players statistics side-by-side using a box plots.

![](doc/source/images/box_plot_chart.png)

In the above screen shot, you see side-by-side vespene per minute statistics.
In this contest, Neeb had the advantage. In addition to the box which shows
the quartiles and the whisker that shows the range, this example has outlier
indicators. In many cases, there will be no outliers.

#### Nelson rules charts

The Nelson rules charts are not so easy. You'll notice quite a bit of code in
helper methods to create these charts.

The base chart is a Bokeh plotting figure with circle markers for each
data point in the time series. This shows the metric over time for
the player. The player charts are side-by-side to allow separate scales
and plenty of additional annotations.

We add horizontal lines to show our x-bar (sample mean), 1st and 2nd standard
deviations and upper and lower control limits for each player.

We use our `detect_nelson_bias()` method to detect 9 or more consecutive points
above (or below) the x-bar line. Then, using Bokeh's `add_layout()` and
`BoxAnnotation`, we color the background green or red for ranges that show
bias for above or below the line respectively.

Our `detect_nelson_trend()` method detects when 6 or more consecutive points
are all increasing or decreasing. Using Bokeh's `add_layout()` and `Arrow`, we
draw arrows on the chart to highlight these up or down trends.

The result is a side-by-side comparison that is jam-packed with statistical
analysis.

![](doc/source/images/nelson_rules_chart.png)

In the above screen shot, you see the time/value hover details that you get
with Bokeh interactive charts. Also notice the different scales and the arrows.
In this contest, Neeb made two early pushes and got an advantage in minerals.
If you run the notebook, you'll see other examples showing where the winner
got the advantage.

### Stored replay documents

You can browse your Cloudant database to see the stored replays. After all
the loading and parsing we stored them as JSON documents. You'll see all
of your replays in the *sc2replays* database and only the latest one in
*sc2recents*.

## 8. Save and share

### How to save your work:

Under the `File` menu, there are several ways to save your notebook:

* `Save` will simply save the current state of your notebook, without any version
  information.
* `Save Version` will save your current state of your notebook with a version tag
  that contains a date and time stamp. Up to 10 versions of your notebook can be
  saved, each one retrievable by selecting the `Revert To Version` menu item.

### How to share your work:

You can share your notebook by selecting the “Share” button located in the top
right section of your notebook panel. The end result of this action will be a URL
link that will display a “read-only” version of your notebook. You have several
options to specify exactly what you want shared from your notebook:

* `Only text and output` will remove all code cells from the notebook view.
* `All content excluding sensitive code cells`  will remove any code cells
  that contain a *sensitive* tag. For example, `# @hidden_cell` is used to protect
  your Bluemix credentials from being shared.
* `All content, including code` displays the notebook as is.
* A variety of `download as` options are also available in the menu.

# Sample output

The sample_output.html in data/examples has embedded JavaScript for
interactive Bokeh charts. Use rawgit.com to view it with the following
link:

[Sample Output](https://cdn.rawgit.com/IBM/starcraft2-replay-analysis/46aed2f7f33b7f9e3a9bd06678a13ba150a42c26/data/examples/sample_output.html)

# Troubleshooting

[See DEBUGGING.md.](DEBUGGING.md)

# License

[Apache 2.0](LICENSE)
