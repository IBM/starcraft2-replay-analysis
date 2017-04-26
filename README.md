# WORK IN PROGRESS

We're still baking this journey, come back in a few weeks to get something fully baked.

# StarCraft II Replay Analysis with Jupyter Notebooks

In this developer journey we will use Jupyter notebooks to analyze
StarCraft II replays and extract interesting insights.

When the reader has completed this journey, they will understand how to:

* Create and run a Jupyter notebook in DSX.
* Use DSX Object Storage to access a replay file.
* Use sc2reader to load a replay into a Python object.
* Examine some of the basic replay information in the result.
* Parse the contest details into a usable object.
* Visualize the contest with Bokey graphics.
* Store the processed replay in Cloudant.

The intended audience for this journey is application developers who
need to process StarCraft II replay files and build powerful visualizations.

![Flow](doc/source/images/architecture.png)

## Included Components

* IBM Data Science Experience (DSX)
* sc2reader
* Object Storage
* Jupyter Notebooks
* Python
* pandas (Python Data Analysis Library)
* NumPy
* Bokeh
* Cloudant NoSQL DB

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
1. [Save and Share](#8-save-and-share)

## 1. Sign up for the Data Science Experience

Sign up for IBM's [Data Science Experience](http://datascience.ibm.com/). By signing up for the Data Science Experience, two services: ``DSX-Spark`` and ``DSX-ObjectStore`` will be created in your Bluemix account.

## 2. Create Bluemix services

Create the following Bluemix services by clicking the **Deploy to Bluemix**
button or by following the links to use the Bluemix UI and create them.

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

## 4. Add the replay file

#### Add the replay to the notebook
Use "Find and Add Data" (look for the 10/01 icon)
and its "Files" tab. From there you can click
"browse" and add a .SC2Replay file from your computer.

#### Create an empty cell
Use the "+" button above to create an empty cell to hold
the inserted code and credentials.

#### Insert to code
After you add the file, use its "Insert to code" drop-down menu.
Make sure your active cell is the empty one created earlier.
Select "Insert StringIO object" from the drop-down menu.

Note: This cell is marked as a hidden_cell because it contains
sensitive credentials.

#### Fix the code!

We don't want to treat the replay as unicode text. We want the bytes.
Change this import:
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

#### Fix-up variable names
The inserted code includes a generated method with credentials and then calls
the generated method to set a variable with a name like "data_1". If you do
additional inserts, the method can be re-used and the variable will change
(e.g. "data_2").

Later in the notebook, we set `replay_file = data_1`. So you might need to
fix the variable name "data_1" to match your inserted code.

## 5. Create a connection to Cloudant

#### Add a new connection to the project
Use the DSX menu to select the project containing the notebook.

Use "Find and Add Data" (look for the 10/01 icon)
and its "Connections" tab. From there you can click "Create Connection".
Give the connection a name and optionally a description.
Under "Service Category" select the "Data Service" button.
Use the "Target service instance" drop-down and select your Cloudant NoSQL DB instance
(e.g., sc2-cloudantNoSQLDB-service).
Make sure the connection you created is enabled with a checkbox in "Connections".


#### Create an empty cell
Use the "+" button above to create an empty cell to hold
the inserted code and credentials.

#### Add the Cloudant credentials to the notebook
Use "Find and Add Data" (look for the 10/01 icon)
and its "Connections" tab. You should see the
connection name created earlier.
Make sure your active cell is the empty one created earlier.
Select "Insert to code" (below your connection name).

Note: This cell is marked as a hidden_cell because it contains sensitive credentials.

#### Fix-up variable names
The inserted code includes a dictionary with credentials assigned to a variable
with a name like "credentials_1". It may have a different name (e.g. "credentials_2").
Rename it or reassign it if needed. The notebook code assumes it will be "credentials_1".

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

## 8. Save and Share

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

TODO

# Troubleshooting

[See DEBUGGING.md.](DEBUGGING.md)

# License

[Apache 2.0](LICENSE)
