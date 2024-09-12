
# Table of Contents
1. [Introduction](#introduction)
2. [Libraries](#libraries)
3. [Loading Data](#loading-data)
4. [Data Exploration](#data-exploration)
5. [Sentiment Analysis](#sentiment-analysis)
6. [Word Cloud Analysis](#word-cloud-analysis)
7. [Emoji Analysis](#emoji-analysis)
8. [Data Collection](#data-collection)
9. [Data Cleaning](#data-cleaning)
10. [Data Export](#data-export)
11. [Results](#results)

## Introduction
This documentation provides a detailed guide to analyze YouTube comments using Python. The process involves loading the data, exploring the data, performing sentiment analysis, creating word clouds, analyzing emojis, collecting additional data, cleaning the data, and exporting the data in various formats.

**Author: Nihar Raju**

## Libraries
The following libraries are used in this code:
- `pandas`: For data manipulation and analysis.
- `numpy`: For numerical operations.
- `matplotlib.pyplot`: For plotting.
- `seaborn`: For statistical data visualization.
- `textblob`: For sentiment analysis.
- `wordcloud`: For generating word clouds.
- `emoji`: For emoji analysis.
- `os`: For file path manipulation.
- `warnings`: For handling warnings.
- `sqlalchemy`: For creating SQL databases.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from textblob import TextBlob as tb
from wordcloud import WordCloud, STOPWORDS
import emoji
import os
import warnings
from warnings import filterwarnings
from sqlalchemy import create_engine
```

## Loading Data
The data is loaded from a CSV file containing YouTube comments.

```python
# Load the data
comments = pd.read_csv(r'N:/Personal_Projects/git/Youtube/UScomments.csv', on_bad_lines="skip")

# Display the first and last few rows of the DataFrame
comments.head()
comments.tail()

# Display information about the DataFrame
comments.info()

# Check for missing values
comments.isnull()
comments.isnull().sum()

# Display the shape of the DataFrame
comments.shape
```

## Data Exploration
The data is explored to understand its structure and check for missing values.

```python
# Create a sample DataFrame with the first 1000 rows
sample_df = comments[0:1000]
sample_df.shape

# Check for missing values and drop them
comments.dropna(inplace=True)
comments.isnull().sum()
```

## Sentiment Analysis
Sentiment analysis is performed on the comments using TextBlob.

```python
# Perform sentiment analysis
polarity = []

for comment in comments['comment_text']:
    try:
        polarity.append(tb(comment).sentiment.polarity)
    except:
        polarity.append(0)

# Add the polarity column to the DataFrame
comments['polarity'] = polarity
comments.head()
```

## Word Cloud Analysis
Word clouds are generated for positive and negative comments.

```python
# Filter positive and negative comments
filter1 = comments['polarity'] == 1
comments_positive = comments[filter1]

filter2 = (comments['polarity'] == -1) | (comments['polarity'] == 0)
comments_negative = comments[filter2]

# Generate word clouds
total_comments_positive = ' '.join(comments_positive['comment_text'])
total_comments_negative = ' '.join(comments_negative['comment_text'])

word_positives = WordCloud(stopwords=set(STOPWORDS)).generate(total_comments_positive)
word_negatives = WordCloud(stopwords=set(STOPWORDS)).generate(total_comments_negative)

# Display word clouds
plt.imshow(word_positives)
plt.axis('off')
plt.imshow(word_negatives)
plt.axis('off')
```

## Emoji Analysis
Emojis are extracted from the comments for analysis.

```python
# Extract emojis from comments
emoji_list = []

for char in comments['comment_text']:
    if char in emoji.EMOJI_DATA:
        emoji_list.append(char)

# Display the first few emojis
emoji_list[1:10]
```

## Data Collection
Additional data files are collected and concatenated into a single DataFrame.

```python
# List all CSV files in the directory
path = r'N:\Personal_Projects\git\Youtube\additional_data-20240803T203430Z-001\additional_data'
files_csv = [f for f in os.listdir(path) if f.endswith('.csv')]

# Initialize an empty DataFrame to store all data
full_df = pd.DataFrame()

# Iterate over each file and concatenate the data
for file in files_csv:
    file_path = os.path.join(path, file)
    try:
        current_df = pd.read_csv(file_path, encoding='iso-8859-1', on_bad_lines='skip')
        print(f"Loaded {file} with shape: {current_df.shape}")
        full_df = pd.concat([full_df, current_df], ignore_index=True)
    except Exception as e:
        print(f"Error loading {file}: {e}")

# Print the shape of the final concatenated DataFrame
print(f"Final concatenated DataFrame shape: {full_df.shape}")

# Display a sample of the final DataFrame to verify contents
print(full_df.head())
```

## Data Cleaning
The concatenated DataFrame is cleaned by removing duplicate rows.

```python
# Check for duplicate rows
full_df.duplicated()
full_df[full_df.duplicated()].shape

# Remove duplicate rows
full_df = full_df.drop_duplicates()
full_df.shape
```

## Data Export
The cleaned DataFrame is exported in various formats (CSV, JSON, SQL).

```python
# Export the DataFrame to a CSV file
full_df.to_csv(r'N:\Personal_Projects\git\Youtube_sample.csv', index=False)

# Export the first 1000 rows to a CSV file
full_df[0:1000].to_csv(r'N:\Personal_Projects\git\Youtube_sample1.csv', index=False)

# Export the first 1000 rows to a JSON file
full_df[0:1000].to_json(r'N:\Personal_Projects\git\Youtube_sample2.json')

# Export the first 1000 rows to an SQL file
engine = create_engine('sqlite:///N:/Personal_Projects/git/Youtube_sample2.sqlite')
full_df[0:1000].to_sql('Users', con=engine, if_exists='append')
```

## Results
The results include the visualizations and analyses performed on the YouTube comments data.

### Visualizations
- **Word Clouds**: Word clouds showing the most frequent words in positive and negative comments.
- **Emoji Analysis**: List of emojis extracted from the comments.

### Analyses
- **Sentiment Analysis**: Polarity scores for the comments.
- **Data Collection**: Concatenated DataFrame from multiple CSV files.
- **Data Cleaning**: Removal of duplicate rows.
- **Data Export**: Exported DataFrame in CSV, JSON, and SQL formats.

**Author: Nihar Raju**
