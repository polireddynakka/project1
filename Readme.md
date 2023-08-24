import pandas as pd

# Given input data
input_data = """
# (your provided data here)
"""

# Split the input data into lines
lines = input_data.strip().split('\n')

# Initialize lists to store data
data = []
current_entry = {}

# Process each line and extract data
for line in lines:
    line = line.strip()
    if line:
        key, value = map(str.strip, line.split(':'))
        current_entry[key] = value
        if key == "Total cost":
            data.append(current_entry)
            current_entry = {}

# Create a DataFrame from the extracted data
df = pd.DataFrame(data)

# Print the DataFrame
print(df)
