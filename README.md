Chaordic ML Challenge
===================

This repository contains my machine learning solution to the challenge proposed by [Chaordic](https://www.chaordic.com.br/). 

In this challenge we had to find out the **gender** of users, based on their **clickstream** data (in one of the many e-commerces that the company's responsible for), and a **catalog** of products. If you wish to know more about the challenge click [here](https://chaordic.github.io/machinelearning-challenge/).

If you only wish to know how to run the code, and understand what's being done by it, jump to the [Understanding the Code](#understanding-the-code) section of this document.

> **Notes:**
>
> - The tools and processes described here were performed under a Windows 7 environment. The code should work on Linux, but it's possible that a few lines of code may have to be changed.
>
> - Python 3.6 was the Python version used during development, so compatibility with Python 2 is not assured.

----------

### Table of Contents

- [Tools Employed](#tools-employed)
- [Files](#files)
	- [Notebooks](#notebooks)
	- ["Outputs" folder](#"outputs"-folder)
	- [CSVs](#csvs)
- [Understanding the Code](#understanding-the-code)
	- [Processing the catalog file](#processing-the-catalog-file)
	- [Processing the target and training data files](#processing-the-target-and-training-data-files)
	- [Running the code](#running-the-code)

----------
Tools Employed
-------------

- Jupyter
- Python 3.6
	- Scikit-learn 0.18.1
	- Pandas 0.20.2
	- XGBoost **\***
	- Matplotlib


> **\*** For Windows installation click [here](https://www.ibm.com/developerworks/community/blogs/jfp/entry/Installing_XGBoost_For_Anaconda_on_Windows?lang=en).



Files
-------------------

#### Notebooks

 - **catalog-treatment**: contains the treatment given to the product's catalog;
 - **data-treatment**: contains the treatment given to the training data and the predictive model utilized;
 - **target-treatment**: contains the treatment given to the training data, so it can be used by the predictive model created;
 - **polished-data-treatment**: same as **data-treatment** file, but comments were added and notebook cells that were confusing/unnecessary were deleted, it also leaves out work done towards models that didn't produce the best results;
 - **polished-target-treatment**: same as **target-treatment** file, but comments were added and notebook cells that were confusing/unnecessary were deleted, it also leaves out work done towards models that didn't produce the best results;

#### "Outputs" folder

This folder houses the **outputs** generated by each iteration of the predictive model. Only files generated by runs that were **submitted** for evaluation are contained here.

#### CSVs

 - **processed_catalog**: file produced by running the **catalog-treatment** notebook;
 - **target_final_data**: file produced by running the **polished-target-treatment** notebook;



Understanding the Code
-------------------

> **Important**: If you want to see all the work that was done for this challenge take a look at the files **target-treatment** and **data-treatment**, but bear in mind that those files are very raw and might be confusing. While they do contain the work that resulted in the best model created here, they also contain code related to different approaches to the data modelling and prediction.
>
>If you don't want to see any of that: ignore these files and work with the files that have **polished** in their name.

#### Processing the catalog file

Since the catalog has more than **20.000** different products it was necessary to reduce its dimensionality, for that end it was decided that the best way to do that would be to implement a unsupervised clustering algorithm, **K-NN** being selected for the first iteration. **K-NN** was chosen for how easy it's to implement and how powerful a starting point it can be.

Considering that the chosen algorithm only works on numerical data, the columns **category** and **sub_category** had their values mapped to integers (**sub_sub_category** wasn't used as to not overfit the model). The value columns had their **NaN** values treated and a new column, **is_sale** was created indicating if the product had a discount or not.

Using the values from: **category**, **sub_category** and **is_sale**, the products were placed into 10 clusters by **K-NN** (new column called **cluster**).

#### Processing the target and training data files

Aggregating each user's data was key, but with so much data it was necessary to inspect each click type beforehand, so information that held no value could be discarded. In the end the rows that had the following values in their **page_type** column were dropped:

 - **cart**
 - **home**
 - **brand_landing**
 - **search**
 - **confirmation**
 - **other**
 - **checkout**
 - **category**
 - **subcategory**

After dropping what was considered as "noise" the data was aggregated over the **uid**, with each product browsed by the user, and each purchase object, being placed on array and, subsequently, on a new column - **products_browsed** and **products_purchased** respectively. Following that the new columns were processed to find out the user's favorite browsing cluster of products, favorite purchase cluster, average spending and if he likes items on sale, which generated new columns for each new information.

Afterwards an **XGBoost** model was fitted by using the columns created (**favorite_purchase_cluster**, **favorite_browse_cluster**, **likes_sales** and **avg_spending**) and **K-Fold Cross Validation** - with **K** being set at **5** folds. Which yielded a model with an average precision score of around **80.7%** with standard deviation of **0.35%**.

#### Running the code

The notebooks should be ran in the following order:

> **catalog-treatment** --> **polished-target-treatment** --> **polished-data-treatment**

Input files should be placed in a new folder called **inputs**, which should be created in the root directory of this project. Any additional files needed by the notebooks will be generated, and loaded, automatically.

When you finish running the notebooks a CSV file called **predictions** should've been created in the root directory of this project. This CSV will have two columns, one for the **user ID** and one for the predicted **gender**.
