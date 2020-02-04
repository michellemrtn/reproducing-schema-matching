# Reproducing schema matching algorithms

The repository contains the algorithms presented in [1] and [2] and reproduced according to the specifications indicated 
in the papers.

[1] [Zhang, Meihui, et al. "Automatic discovery of attributes in relational databases." Proceedings of the 2011 ACM SIGMOD International Conference on Management of data. 2011.](https://dl.acm.org/doi/pdf/10.1145/1989323.1989336?casa_token=rBsHeImB_M8AAAAA:XW3PK9oDVGKSXtuIgbLkE-R2VyE1_Ym2SOoRvx3puR2BE2kSASiPHGGs3hDWrFizLK5B6DZjkLnA)

[2] [Madhavan, Jayant, Philip A. Bernstein, and Erhard Rahm. "Generic schema matching with cupid." vldb. Vol. 1. 2001.
](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/tr-2001-58.pdf)

The repository contains the [algorithms](algorithms), [experiments](experiments) and the [data](data) used in the experiments.

## Install
> The project has been developed on MacOS Catalina and Ubuntu 19.10
#### Prerequisites 
* The algorithms require Python3.6 or Python3.7 and pip
* Some packages require C++. Make sure you have it in your OS before installing the requirements.
> For Windows 10+, you should install Virtual Studio and add the C++ package from there.

> There are some more issues regarding building the packages using pip on Windows. To fix them, follow the next steps:
```
Copy these files:

rc.exe
rcdll.dll

From

C:\Program Files (x86)\Windows Kits\8.1\bin\x86 (or similar)

To

C:\Program Files (x86)\Microsoft Visual Studio 11.0\VC\bin (or similar)
```

#### Install packages
* We advise to create a virtual environment using your preferred method.

* Install the requirements:
```
pip install -r requirements.txt
```

## Run
### Cupid [2]
In the [experiments](experiments) folder, the [cupid_experiments](experiments/cupid_experiments.py) file
contains the method to run the experiments in order to find the proper thresholds and create plots
to visualise the precision, recall and f1-score. 

An example on how to run the experiments is in [cupid_cupid_data](experiments/cupid_cupid_data.py) which uses the 
[data example](data/cupid/paper) indicated in the paper [1]. 
* Read your data in your preferred method;
* Create a "Cupid" object 
```python
cupid_model = Cupid()
```
* Add the data: schema_name, table_name, pairs of column_name, data_tye. **Note**: schema name should be different for two datasets
```python
cupid_model.add_data(schema_name, table_name, (column_name, data_type))
```
* Next, decide on a source tree and a target tree
```python
source_tree = cupid_model.get_schema_by_index(0)
target_tree = cupid_model.get_schema_by_index(1)
```
* Indicate the output directory (for the results), the gold standard file (for computing the statistics - precision, recall, F1-score)
and two intervals for thresholds (for more details about the thresholds see [2]):
    * leaf range - typical lower than 1.0
    * accept threshold - typical value = 0.5, but you can experiment with lower values
```python
out_dir = CURRENT_DIR + '/cupid-output/'
gold_standard_file = CURRENT_DIR + '/cupid-paper-gold.txt'
leaf_range = np.arange(0.1, 1, 0.1)
th_accept_range = np.arange(0.05, 0.5, 0.02)
```
* Run experiments:
```python
run_experiments(source_tree, target_tree, cupid_model, out_dir, leaf_range, th_accept_range)
```
* If you have a gold standard file, compute the statistics - make sure you provide the matchings as tuples (see [cupid-paper-gold](experiments/cupid-paper-gold.txt)):
```python
compute_statistics(gold_standard_file, out_dir, leaf_range, th_accept_range)
```