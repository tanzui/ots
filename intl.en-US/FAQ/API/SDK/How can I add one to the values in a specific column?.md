# How can I add one to the values in a specific column?

The following code provides an example on how to add one to the values in a specified column:

```
row = getRow(primary_key, 'col') // Read the values in the column.
old_value = row['col'] // Record the original values in the column.
row['col'] = old_value + 1 // Calculate new values.
updateRow(row, condition: row['col'] == old_value) // Use conditional update when you write new values. Condition: The data is written when the original values remain unchanged.
		
```

