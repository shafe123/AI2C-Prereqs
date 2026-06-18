# NumPy and pandas

This repository introduces the essential Python libraries for data analysis—**NumPy** and **pandas**—which provide the speed and functionality required for large-scale data manipulation beyond the capabilities of pure Python.


## 🎯 Lesson Objectives

* Understand the role of **modules, packages, and libraries** in Python.
* Master the basics of **NumPy** for numerical computing.
* Utilize **pandas** for structured data analysis and manipulation.
* Create basic data visualizations using **Matplotlib**.


## 🏗️ Key Python Terminology

Understanding how Python organizes code is fundamental to using these tools effectively.

- **Module:** A single `.py` file containing related functions, classes, and variables to promote code reuse.
- **Package:** A directory hierarchy of related modules, identified by an `__init__.py` file.
- **Library:** A broad collection of packages and modules offering wide-ranging functionality (e.g., the Python Standard Library).


## 🔢 NumPy: Numerical Python

NumPy is the foundational library for scientific computing in Python, offering powerful N-dimensional array objects.

- **ndarray:** The core data structure; a fast and flexible container for large datasets of the same type.
- **Efficiency:** NumPy operations are implemented in C, making them significantly faster than standard Python lists for mathematical operations.
- **Vectorization:** Allows you to perform operations on entire arrays without writing explicit `for` loops.


## 🐼 pandas: Data Analysis Library

Pandas provides high-performance, easy-to-use data structures like **DataFrames** (2D tables) and **Series** (1D arrays).


### Common Data Operations

- **Loading Data:** Import data from various formats using functions like `pd.read_csv()`.
- **Data Cleaning:** Rename columns (`.rename()`), drop unnecessary data (`.drop()`), and handle missing values.
- **Manipulation:**
  - **Filtering:** Selecting specific rows based on conditions.
  - **Grouping:** Aggregating data by categories using `.groupby()`.
  - **Sorting:** Ordering data with `.sort_values()`.
- **Analysis:** Perform arithmetic on entire columns or apply custom functions using `.apply()`.


## 📊 Data Visualization with Matplotlib

Matplotlib is a powerful library for creating static, animated, and interactive visualizations in Python.

- **Integration:** Works seamlessly with NumPy arrays and pandas DataFrames (e.g., `df.plot()`).
- **Customization:** Offers full control over colors, labels, legends, and styles.
- **Chart Types:** Supports line graphs, bar charts, scatter plots, and histograms.


## 🧪 Final Practice Exercise

To test these skills, students analyze a dataset of top-ranking women's concert tours (`top_20_womens_tours.csv`) to perform the following tasks:

1. Clean and rename specific columns.
2. Filter the data for specific artists (e.g., Taylor Swift).
3. Convert currency strings to floats for calculation.
4. Perform aggregations to find total and average gross revenues.
 
