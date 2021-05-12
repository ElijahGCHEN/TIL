- loc/iloc v.s. slicing

For `dataframe`, use `loc` and/or `iloc` when indexing rows **and** columns. If indexing on row **or** column, you can get away without it, and is referred to as 'slicing'.

- Dealing with `nan`

For a `dataframe` `df`, the nan values can be captured and dealt as follows:
```python
df_indices = np.isnan(df)
df[df_indices] = # operations here

# Calculating the average as a simple example
df_avg = np.average(df[~df_indices])
```

- Iterating over rows

For a `dataframe` `df`, it can be done as follows:
```python
for index, row in df.iterrows():
  #operations here
```
It should be noticed that the return value of `iterrows()` is a `Series` with both index and the values.
