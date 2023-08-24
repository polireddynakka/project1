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
    
    current_service = None
    for line in lines:
        if line.startswith(" . "):  # Service cost entry
            service_parts = line.split(" - Cost: ")
            service_name = service_parts[0].strip(" .")
            cost = float(service_parts[1].split(":")[1].strip().replace("$", ""))
            record_dict["Cost_Usage_by_Service"][service_name] = cost
        else:
            key, value = line.split(":", 1)
            record_dict[key.strip()] = value.strip()
    
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


key, value = line.split(":", 1)
            record_dict[key.strip()] = value.strip()

