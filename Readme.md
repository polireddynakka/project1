# Split the input data into individual records
input_data = """
# The input data you provided
"""

# Splitting the input data into individual records based on the "apm_id" lines
records = input_data.strip().split("apm_id:")

# Initialize an empty list to store dictionaries for each record
record_dicts = []

# Process each record and extract information
for record in records[1:]:
    lines = record.strip().split("\n")
    record_dict = {
        "Cost_Usage_by_Service": {}
    }
    
    for line in lines:
        key_value = line.strip().split(":", 1)
        if len(key_value) == 2:
            key = key_value[0].strip()
            value = key_value[1].strip()
            
            if key.startswith(". "):  # Service cost entry
                service_name, cost = value.split(" - Cost: ")
                record_dict["Cost_Usage_by_Service"][service_name] = float(cost.replace("$", ""))
            else:
                record_dict[key] = value
    
    record_dicts.append(record_dict)

# Display the data in a tabular format
for record_dict in record_dicts:
    print("APM_ID:", record_dict.get("apm_id", "N/A"))
    print("Account:", record_dict.get("Account", "N/A"))
    print("Application Name:", record_dict.get("application_name", "N/A"))
    print("Month:", record_dict.get("Month", "N/A"))
    print("Total Cost:", record_dict.get("Total cost", "$0.00"))
    
    print("\nCost Usage by Service:")
    print("-" * 54)
    cost_by_service = record_dict.get("Cost_Usage_by_Service", {})
    for service, cost in cost_by_service.items():
        print(f"{service} | Cost: ${cost:.2f}")
    
    print("=" * 54)


    service_name, cost = value.split(" - Cost: ")
ValueError: not enough values to unpack (expected 2, got 1)
