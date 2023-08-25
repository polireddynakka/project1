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



Pid": 0,
            "ExitCode": 126,
            "Error": "failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: error mounting \"/folder\" to rootfs at \"/usr/local/test/folder\": stat /proc/self/fd/13: permission denied: unknown",
            "StartedAt": "0001-01-01T00:00:00Z",
            "FinishedAt": "0001-01-01T00:00:00Z"

#!/bin/bash

# Enable the user_allow_other option in /etc/fuse.conf
sed -i '/^#user_allow_other/s/^#//' /etc/fuse.conf


https://stackoverflow.com/questions/17544139/allowing-permission-using-s3fs-bucket-directory-for-other-users




import requests

# Replace this with your actual Microsoft Teams webhook URL
webhook_url = "https://your_teams_webhook_url_here"

# Your main data in a structured format
main_data = [
    {"Account": "993514063544", "Application Name": "Provider-Finder-Slvr", "Month": "July", "Total Cost": "$0.00"},
    {"Account": "339644262595", "Application Name": "Provider-Finder-Gold", "Month": "July", "Total Cost": "$29156.92"},
    # ... (other main data entries)
]

# Your service data in a structured format
service_data = [
    {"Account": "339644262595", "Application Name": "Provider-Finder-Gold", "Service Name": "AWS Glue", "Cost": "$471.65"},
    # ... (other service data entries)
]

# Create formatted tables for main data and service data
main_formatted_table = "\n".join([
    f"| {entry['Account']: <13} | {entry['Application Name']: <21} | {entry['Month']: <6} | {entry['Total Cost']: <11} |"
    for entry in main_data
])

service_formatted_table = "\n".join([
    f"| {entry['Account']: <13} | {entry['Application Name']: <21} | {entry['Service Name']: <30} | {entry['Cost']: <11} |"
    for entry in service_data
])

# Prepare the adaptive card payload for main data
main_adaptive_card = {
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "type": "AdaptiveCard",
    "version": "1.4",
    "body": [
        {
            "type": "TextBlock",
            "text": "Main Data Table",
            "weight": "bolder",
            "size": "large"
        },
        {
            "type": "TextBlock",
            "text": main_formatted_table,
            "wrap": True
        }
    ]
}

# Prepare the adaptive card payload for service data
service_adaptive_card = {
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "type": "AdaptiveCard",
    "version": "1.4",
    "body": [
        {
            "type": "TextBlock",
            "text": "Service Data Table",
            "weight": "bolder",
            "size": "large"
        },
        {
            "type": "TextBlock",
            "text": service_formatted_table,
            "wrap": True
        }
    ]
}

# Prepare the message payload with both adaptive cards
message = {
    "type": "message",
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": main_adaptive_card
        },
        {
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": service_adaptive_card
        }
    ]
}

# Send the message using the webhook URL
response = requests.post(webhook_url, json=message)

# Check if the request was successful
if response.status_code == 200:
    print("Message sent successfully to Teams!")
else:
    print(f"Error sending message to Teams. Status code: {response.status_code}")
