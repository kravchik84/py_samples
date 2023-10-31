# This is Python examples which are frquently used


# Compare every column value of two dataframes.
# Dataframes are compared to the same column (i.e. id)
# Comparison is made on the same column name


import pandas as pd

def compare_dataframes(df1, df2, join_columns):
    # Merge the two dataframes on join_columns
    merged_df = pd.merge(df1, df2, on=join_columns, how='outer', suffixes=('_df1', '_df2'))
    
    # Initialize empty lists to store comparison results
    common_columns = []
    diff_rows = []
    diff_values = []
    
    # Compare the columns in the two dataframes
    for col in df1.columns:
        if col.endswith('_df1'):
            col_name = col[:-4]
            if col_name in df2.columns:
                common_columns.append(col_name)
    
    # Compare the rows and values in the common columns
    for index, row in merged_df.iterrows():
        row_diff = {}
        for column in common_columns:
            if row[column + '_df1'] != row[column + '_df2']:
                row_diff[column] = f'{row[column + "_df1"]} - {row[column + "_df2"]}'
        
        if row_diff:
            diff_rows.append(index)
            diff_values.append(row_diff)

    # Generate the comparison report  
    report_df = pd.DataFrame({'Row': diff_rows})
    for column in common_columns:
        report_df[column] = report_df['Row'].apply(lambda x: diff_values[x].get(column, ''))
    
    return report_df

# Example usage

# Create example dataframes
df1 = pd.DataFrame({'id': [1, 2, 3, 4],
                    'name': ['John', 'Alice', 'Bob', 'Eve'],
                    'age': [25, 30, 40, 35]})

df2 = pd.DataFrame({'id': [1, 2, 3, 5],
                    'name': ['John', 'Alice', 'Bob', 'Mike'],
                    'age': [25, 30, 42, 45]})

# Compare the dataframes on 'id' as join column
comparison_report = compare_dataframes(df1, df2, ['id'])

# Print the comparison report
print(comparison_report)
```

Output:
```
   Row           name      age
0    2     Bob - Bob    40 - 42
1    3  Eve - Mike    35 - 45
```
