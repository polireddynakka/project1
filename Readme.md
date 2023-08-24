# Input data as a string
input_data = """
apm_id: APM1003015
Account: 339644262595
application_name: Provider-Finder-Gold
Month: July 
Cost_Usage_by_Service: 
. AWS Glue - Cost: $471.65
. AWS Key Management Service - Cost: $17.42
. AWS Lambda - Cost: $0.00
. AWS Secrets Manager - Cost: $0.00
. Amazon Athena - Cost: $0.01
. EC2 - Other - Cost: $136.45
. Amazon Elastic Compute Cloud - Compute - Cost: $5904.18
. Amazon OpenSearch Service - Cost: $22478.67
. Amazon Simple Storage Service - Cost: $133.06
. AmazonCloudWatch - Cost: $15.48
Total cost: $29156.92
"""

# Split the input data into lines
lines = input_data.strip().split('\n')

# Initialize variables to store parsed data
data_entry = {}
data_entries = []

# Parse the input lines
for line in lines:
    key, value = line.split(': ', 1)
    if key == 'apm_id':
        if data_entry:  # If data_entry is not empty, store it in data_entries
            data_entries.append(data_entry)
        data_entry = {"apm_id": value}
    elif key == 'Total cost':
        data_entry[key] = value
        data_entries.append(data_entry)
        data_entry = {}
    else:
        data_entry[key] = value

# Print the parsed data entries
for entry in data_entries:
    print(entry)

