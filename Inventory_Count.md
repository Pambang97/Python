# Python
import pandas as pd

# Import from the CSV location
file = r'C:\Users\ptorres\Downloads\Consultation_Daily_Log.csv'
df = pd.read_csv(file)

# Get the list of columns that contain 'Dispensed Drug'
dispensed_cols = [col for col in df.columns if 'Dispensed Drug' in col]

# Combine all values from those columns into a single Series (one column)
combined_series = pd.concat([df[col] for col in dispensed_cols], ignore_index=True)

# Convert it back to a DataFrame
combined_df = combined_series.to_frame(name='Dispensed Drug')

# Now split the combined 'Dispensed Drug' column into multiple columns by comma
split_df = combined_df['Dispensed Drug'].str.split(',', expand=True)

# Remove empty rows
split_df.dropna(how='all', inplace=True)

# Convert column 3 to numeric (coerce errors to NaN)
split_df[3] = pd.to_numeric(split_df[3], errors='coerce')

# Group by both column 0 and 1, and sum column 3
unique_pair_sums = split_df.groupby([0, 1, 2])[3].sum().reset_index()

# Rename columns for clarity (optional)
unique_pair_sums.columns = ['Chemical', 'Dosage', 'Dosage Form', 'Total_Qty']

print(unique_pair_sums)
