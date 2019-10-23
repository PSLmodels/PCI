Website: [policychangeindex.org](https://policychangeindex.org)

[![Build Status](https://travis-ci.com/PSLmodels/PCI-China.svg?branch=master)](https://travis-ci.com/PSLmodels/PCI-China) [![codecov](https://codecov.io/gh/PSLmodels/PCI/branch/master/graph/badge.svg)](https://codecov.io/gh/PSLmodels/PCI-China)

Authors: [Julian TszKin Chan](https://sites.google.com/site/ctszkin/) and [Weifeng Zhong](https://www.weifengzhong.com)

Please email all comments/questions to ctszkin [AT] gmail.com or weifeng [AT] weifengzhong.com

What is the Policy Change Index (PCI) for China?
-----------------------------------------------
China's industrialization process has long been a product of government direction, be it coercive central planning or ambitious industrial policy. For the first time in the literature, we develop a quantitative indicator of China's policy priorities over a long period of time, which we call the Policy Change Index (PCI) for China. The PCI is a leading indicator that runs from 1951 to the most recent quarter and can be updated in the future. In other words, the PCI not only helps us understand the past of China's industrialization but also allows us to make short-term predictions about its future directions.

The design of the PCI has two building blocks: (1) it takes as input data the full text of the *People's Daily* --- the official newspaper of the Communist Party of China --- since it was founded in 1946; (2) it employs a set of machine learning techniques to "read" the articles and detect changes in the way the newspaper prioritizes policy issues.

The source of the PCI's predictive power rests on the fact that the *People's Daily* is at the nerve center of the China's propaganda system and that propaganda changes often precede policy changes. Before the great transformation from the central planning under Mao to the economic reform program after Mao, for example, considerable efforts were made by the Chinese government to promote the idea of reform, move public opinion, and mobilize resources toward the new agenda. Therefore, by detecting (real-time) changes in propaganda, the PCI is, effectively, predicting (future) changes in policy.

For details about the methodology and findings of this project, please see the following research paper:

- Chan, Julian TszKin and Weifeng Zhong. 2019. "Reading China: Predicting Policy Change with Machine Learning." AEI Economics Working Paper [No. 2018-11](http://www.aei.org/wp-content/uploads/2018/10/Reading-China-AEI-WP.pdf) (latest version available [here](https://policychangeindex.org/Reading_China.pdf)).


Disclaimer
----------
Results will change as the underlying models improve. A fundamental reason for adopting open source methods in this project is so that people from all backgrounds can contribute to the models that our society uses to assess and predict changes in public policy; when community-contributed improvements are incorporated, the model will produce better results.


Getting Started
---------------
The first step for everyone (users and developers) is to open a free GitHub account. And then you can specify how you want to "watch" the PCI repository by clicking on the Watch button in the upper-right corner of the repository's main page.

The second step is to get familiar with the PCI repository by reading the documentation.

If you want to ask a question or report a bug, create a new issue [here](https://github.com/PSLmodels/PCI/issues) and post your question or tell us what you think is wrong with the repository.

If you want to request an enhancement, create a new issue [here](https://github.com/PSLmodels/PCI/issues) and provide details on what you think should be added to the repository.


Usage
---------------
Three python functions and an R script are available for users to process data and build the neural network models to construct the PCI for China.

- `proc_pd.py`:              Process and prepare the raw data from the *People's Daily* for building the neural network models.
- `pci.py`:                    Train a neural network model to construct the PCI for a specified year-quarter.
- `compile_model_results.py`:  Compile the results from all models and export them to a `.csv` file.
- `generate_figures.r`:        Generate figures.

Users can check out the descriptions of the arguments for each python function using the `--help` option. For example:

```{shell}
./PCI-China/data>python proc_pd.py --help
Using TensorFlow backend.
usage: proc_pd.py [-h] [--input INPUT] [--create CREATE] [--seed SEED]
                  [--k_fold K_FOLD] [--output OUTPUT]

optional arguments:
  -h, --help       show this help message and exit
  --input INPUT    Input file
  --create CREATE  1:Create database 0:Append
  --seed SEED      Seed
  --k_fold K_FOLD  k_fold
  --output OUTPUT  Output filename
```

```{shell}
./PCI-China>python pci.py --help
Using TensorFlow backend.
usage: pci.py [-h] [--model MODEL] [--year YEAR] [--month MONTH] [--gpu GPU]
              [--iterator ITERATOR] [--root ROOT] [--temperature TEMPERATURE]
              [--discount DISCOUNT] [--bandwidth BANDWIDTH]

optional arguments:
  -h, --help            show this help message and exit
  --model MODEL         Model name: window_5_years_quarterly,
                        window_10_years_quarterly, window_2_years_quarterly
  --year YEAR           Target year
  --month MONTH         Target month
  --gpu GPU             Which gpu to use
  --iterator ITERATOR   Iterator in simulated annealing
  --root ROOT           Root directory
  --temperature TEMPERATURE
                        Temperature in simulated annealing
  --discount DISCOUNT   Discount factor in simulated annealing
  --bandwidth BANDWIDTH
                        Bandwidth in simulated annealing```

```{shell}
./PCI-China>python compile_model_results.py --help
usage: compile_model_results.py [-h] [--model MODEL] [--root ROOT]

optional arguments:
  -h, --help     show this help message and exit
  --model MODEL  Model name: window_5_years_quarterly,
                 window_10_years_quarterly
  --root ROOT    Root folder
```

Data
----
The raw data of the *People's Daily*, which are not provided in this repository, should be placed in the sub-folder `PCI-China/Input/pd/`. Each file in this sub-folder should contain one year-quarter of data, be named by the respective year-quarter, and be in the `.pkl` format. For example, the raw data for the first quarter of 2018 should be in the file `2018_Q1.pkl`. Below is the list of column names and types of each raw data file:

```{python}
>>> df1 = pd.read_pickle("./PCI-China/Input/pd/pd_1946_1975.pkl")
>>> df1.dtypes
date     datetime64[ns]
year              int64
month             int64
day               int64
page              int64
title            object
body             object
id                int64
dtype: object
```

where `title` and `body` are the Chinese texts of the title and body of each article.

The processed data of the *People's Daily*, which are not provided in this repository, should be placed in the sub-folder `PCI-China/data/proc/by_year/`. Each file in this sub-folder should contain one year-quarter of data, be named by the respective year-quarter, and be in the `.pkl` format. For example, the processed data for the first quarter of 2018 should be in the file `2018_Q1.pkl`. Provided with the raw data, the processed data are the output of running `proc_data.sh`. Below is the list of column names and types of each processed data file:

```{python}
import sqlite3
import pandas as pd 

conn = sqlite3.connect("data/output/database.db")
pd.read_sql_query("PRAGMA TABLE_INFO(main)", conn)
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cid</th>
      <th>name</th>
      <th>type</th>
      <th>notnull</th>
      <th>dflt_value</th>
      <th>pk</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>date</td>
      <td>TIMESTAMP</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>id</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>page</td>
      <td>REAL</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>title</td>
      <td>TEXT</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>body</td>
      <td>TEXT</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>strata</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>title_seg</td>
      <td>TEXT</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>body_seg</td>
      <td>TEXT</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>year</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>quarter</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>month</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>day</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>weekday</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>frontpage</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>page1to3</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>title_len</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>body_len</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>n_articles_that_day</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>n_pages_that_day</td>
      <td>REAL</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>n_frontpage_articles_that_day</td>
      <td>INTEGER</td>
      <td>0</td>
      <td>None</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>





where `title_int` and `body_int` are the word embeddings (numeric vectors) of the title and body of each article.


Citing the Policy Change Index (PCI) for China
---------------------------------------------

Please cite the source of the latest Policy Change Index (PCI) for China by the website: https://policychangeindex.org.

For academic work, please cite the following research paper:

- Chan, Julian TszKin and Weifeng Zhong. 2019. "Reading China: Predicting Policy Change with Machine Learning." AEI Economics Working Paper [No. 2018-11](http://www.aei.org/wp-content/uploads/2018/10/Reading-China-AEI-WP.pdf) (latest version available [here](https://policychangeindex.org/Reading_China.pdf)).
