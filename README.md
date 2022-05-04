[![Repo Checks](https://github.com/Sta663-Sp22/hw03_team12/workflows/Repo%20Checks/badge.svg)](https://github.com/Sta663-Sp22/hw03_team12/actions?query=workflow:%22Repo%20Checks%22)


Homework 3 - Parking Wars: Manhattan
---
due Friday, March 18th by 5:00 pm


* Huang, Nancy - [ranxin.huang@duke.edu](mailto:ranxin.huang@duke.edu)
* Pliego San Martin, Jose - [jose.pliego.san.martin@duke.edu](mailto:jose.pliego.san.martin@duke.edu)
* Reynolds, Alison - [alison.reynolds@duke.edu](mailto:alison.reynolds@duke.edu)
* Wang, Jingjing - [jingjing.wang386@duke.edu](mailto:jingjing.wang386@duke.edu)


![ticket](nyc_parking_ticket.jpg?raw=true)

## Background

New York City is at the forefront of the open data movement among local, state and federal governments. They have made publicly available a huge amount of data ([NYC Open Data](https://nycopendata.socrata.com/)) on everything from street trees, to restaurant inspections, to parking violations. It is the last of these that we will be focusing on for this homework assignment. 

The original data is quite large (2.01 GB uncompressed CSV file for the 2019 data) and contains more than 9 million observations. This is not so big that you can't run our analyses on a moderately powerful laptop, but it is large enough that ypu need to pay attention to what you do with the data and the performance of your code. These data contains reflect parking violations from all five boroughs of New York City for fiscal years 2019. We have simplified that data significantly by removing all records not from Manhattan and only retaining features having to do with police precincts and ticket locations.

The police precincts in New York are numbered, the following 22 precincts are found in Manhattan: 1, 5, 6, 7, 9, 10, 13, 14, 17, 18, 19, 20, 22, 23, 24, 25, 26, 28, 30, 32, 33, 34.

<br/>

## Data

The violation data along with supplementary data is available on in the `data/` directory of your repository. 

* `manh_tickets_2019.csv.zip` - all parking violations for fiscal year 2019 in Manhattan. Note that the file is zipped to make it small enough to be posted on GitHub - it can still be directly read in using `pd.read_csv()` without needing to unzip it first. 
([source](https://data.cityofnewyork.us/City-Government/Parking-Violations-Issued-Fiscal-Year-2019/faiq-9dfq))

* `manh_pluto.csv` - a csv file containing spatial locations for taxed addresses in Manhattan. Each row contains an address and the latitude and longitude of each property (the centroid of the properties boundary) and is intended to be used for geocoding the parking tickets. 
([source](http://www.nyc.gov/html/dcp/html/bytes/dwn_pluto_mappluto.shtml#mappluto))

* `manh_boundary.csv` - a csv file containing the boundary of the main island of Manhattan (other smaller islands have been removed). 

* `manh_pred.csv` - a csv file containing latitude and longitude of ~23,000 evenly spaced prediction locations within Manhattan.

<br/>

## Task 1 - Geocoding

The original parking violation data contains a large number of variables that we do not care about for this assignment. Using the simplified version of this data your task is to attempt to geocode (find latitudes and longitudes) for as many of the parking tickets as possible using the given variables. Note that this data has had minimal cleaning done, there are a large number of errors, omissions, and related issues. Also, note that each file is still quite large, over 3.7 million tickets just for Manhattan, so even under the most optimistic of circumstances you will not be able to, nor should you, use any of the standard web based geocoding services.

In order to be successful at this task you do not need to geocode every address, or even most addresses. The goal is to geocode as many as possible with as much accuracy as possible to enable you to be successful with the 2nd task. This is a messy and large data set and at the best of times geocoding is a very difficult problem - go for the low hanging fruit first and then work on the edge cases / exceptions later as needed. The majority of this work would consistent of cleaning and fixing the address data in `manh_tickets_2019` and `manh_pluto` and then merging the two data sets based on the values in their `address` columns.

Your write up for this task should include a description of any and all cleaning / subsetting / etc. that was done to the data, as well as a description of your geocoding approach(es) and a discussion of how successful each was. 

<br/>

## Task 2 - Recreating NYC's Police Precincts

The ultimate goal of this assignment is to reconstruct the boundaries of all 22 Manhattan New York City police precincts (numbered between 1 and 34). The parking violation data set contains the column, `precinct`, that lists the police precinct in which the violation ostensibly took place (these can be and are mis-entered in the data). Your goal is to take this data along with the geocoded locations from Task 1 and build a model which is capable of predicting boundaries for all 22 precincts.

As mentioned before, the data is complex and messy so keep in mind that there is no guarantee that the reported precinct is correct, or the street address is correct. As such, the goal is not perfection, anything that even remotely resembles the precinct map will be considered a success. No single approach for this estimation is likely to work well, and an iterative approach to cleaning and tweaking will definitely be necessary. I would suggest initially focusing on a single precinct to develop your methods before generalizing to the entirety of Manhattan. 

You should treat this as a multivariate classification problem where the labels come from your geocoding and the features are latitude, longitude, and potentially any transformations / interactions of these. You should try multiple different modeling approaches for these data, however your final submission should only include one model. Your write up should include a discussion of the other approaches tried and why they were rejected and why your final model was selected. All modeling must be performed with scikit-learn - you may use any supported modeling approach for this task.

Ideally, all tuning of model hyperparameters should be principled and based on appropriate selection using cross validation. It is strongly suggested that you consider stratifying your cross-validation approach by precinct. The range of values used for searching during hyperparameter tuning should be discussed in your write up.

There is a hard limit of 60 minute run time for this assignment, we should be able to run all your code and have the entire analysis and all output finished within this time frame. If you find yourself consistently exceeding this consider reducing run time by selecting a different model, reducing the total number of models that need to be fit (e.g. reducing the number of folds used with KFolds), and or restricting the range of values being explored during hyperparameter tuning. Any necessary trade offs for meeting this runtime goal should be discussed in the write up.


### Central Park

The 13th precinct in Manhattan covers central park, this precinct will be particularly challenging since there are very few commercial buildings for us to use as a basis of geocoding. As such you will likely have almost no points for this particular precinct coming from your Task 1 results. 

For this and only this precinct you are allowed to impute fake ticket locations and add them to your data frame from Task 1. You may do this by finding the coordinates of the four corners of Central Parking and using those to construct a simple boundary from which you sample interior points. 


### Work Product

At the end of this task you should have a fitted model / pipeline which can be used to predict the most likely precicnt for any latitude and longitude coordinate in Manhattan. Specifically, you should be able to predict from the data contained in `manh_pred.csv`.

Your write up for this section should include all of the details described above as well as a specific discussion of the metric(s) used for model tuning and an assessment of your models overall performance based on your geocoded data (this should include at a minimum the full confusion matrix for your model for out an out of sample prediction).

<br/>

## Task 3 - Visualize the boundaries

Using your model from Task 2 and the prediction locations contained in `manh_pred.csv` create a visualization showing your predicted boundaries across Manhattan. If you would like you can also use the boundary data that is included in `manh_bound.csv`. Additionally, if feasible show some if not all of the training data. 

See the following scikit-learn examples for inspiration: [k-means](https://scikit-learn.org/stable/auto_examples/cluster/plot_kmeans_digits.html#sphx-glr-auto-examples-cluster-plot-kmeans-digits-py), [decision trees](https://scikit-learn.org/stable/auto_examples/tree/plot_iris_dtc.html#sphx-glr-auto-examples-tree-plot-iris-dtc-py), [logistic regression](https://scikit-learn.org/stable/auto_examples/linear_model/plot_iris_logistic.html#sphx-glr-auto-examples-linear-model-plot-iris-logistic-py) + others

No write up is needed for this task.

<br/>

## Submission and Marking

This homework is due on Friday, March 18th by 5:00 pm. You are to
complete the assignment as a team and to keep everything (code, write
ups, etc.) in your team’s repository (commit early and often). Only the
work committed to the repositories’ main branch by the deadline will be
marked. As mentioned in syllabus, all team members are expected to
contribute equal effort for this assignment - individual contributions
will be assessed after the assignment is completed via peer evaluations
and commits.

The final product for this assignment should be a single notebook
(hw3.ipynb) that contains all code and text for the tasks described
above. This document should be clearly and cleanly formatted and present
all of your results. Style and formatting does count for this
assignment, so please take the time to make sure everything looks good
and your text and code are properly formatted. This document must be
reproducible and we must be able to rerun it with minimal intervention -
documents that do not compile will be penalized.

Given the expected runtimes for this assignment, we will not be automatically 
running checks on the repository each time you push - instead you will need to 
manually trigger the action by doing the following:

Opening the Actions tab on your repo, select the Repo Checks workflow, and then click on the "Run workflow" button on the right of the blue banner in the middle of the page. This will create a pop-up with a green "Run workflow" button which will then launch the action (you can also trigger the action for other branches if you are using them). Once launched you will be able to see the running action by reloading the page.