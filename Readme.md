from prettytable import PrettyTable

# Data for your table
data = [
    {"Account": "993514063544", "Application Name": "Provider-Finder-Slvr", "Month": "July", "Total Cost": "$0.00"},
    {"Account": "339644262595", "Application Name": "Provider-Finder-Gold", "Month": "July", "Total Cost": "$29156.92"},
    # ... (other data entries)
]

# Create the main table
main_table = PrettyTable()
main_table.field_names = ["Account", "Application Name", "Month", "Total Cost"]

for entry in data:
    main_table.add_row([entry["Account"], entry["Application Name"], entry["Month"], entry["Total Cost"]])

# Data for your service-specific tables
service_data = [
    {"Account": "339644262595", "Application Name": "Provider-Finder-Gold", "Service Name": "AWS Glue", "Cost": "$471.65"},
    # ... (other service data entries)
]

# Create the service-specific tables
service_tables = {}

for entry in service_data:
    key = (entry["Account"], entry["Application Name"])
    if key not in service_tables:
        service_tables[key] = PrettyTable()
        service_tables[key].field_names = ["Account", "Application Name", "Service Name", "Cost"]
    service_tables[key].add_row([entry["Account"], entry["Application Name"], entry["Service Name"], entry["Cost"]])

# Print the tables
print("Main Table:")
print(main_table)
print()

print("Service-Specific Tables:")
for table in service_tables.values():
    print(table)
    print()
