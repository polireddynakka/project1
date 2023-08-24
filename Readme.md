import requests
from tabulate import tabulate

# Replace with your actual Microsoft Teams webhook URL
teams_webhook_url = "YOUR_TEAMS_WEBHOOK_URL"

# Sample data
data = [
    {
        "apm_id": "APM1003015",
        "Account": "339644262595",
        "application_name": "Provider-Finder-Gold",
        "Month": "July",
        "Cost_Usage_by_Service": {
            "AWS Glue - Cost": "$471.65",
            "AWS Key Management Service - Cost": "$17.42",
            # ... other services
        },
        "Total cost": "$29156.92"
    },
    # ... other data entries
]

# Format data into a table
table_data = []
for entry in data:
    table_data.append([
        entry["apm_id"],
        entry["Account"],
        entry["application_name"],
        entry["Month"],
        entry["Total cost"]
    ])

table_headers = ["apm_id", "Account", "application_name", "Month", "Total cost"]
table = tabulate(table_data, headers=table_headers, tablefmt="pipe")

# Send formatted data to Teams
message = {
    "text": "Cost Data Summary:",
    "sections": [
        {
            "activityTitle": "Cost Data Summary",
            "facts": [
                {
                    "name": "Month",
                    "value": "July"
                }
            ],
            "text": f"```\n{table}\n```"
        }
    ]
}

headers = {
    "Content-Type": "application/json"
}

response = requests.post(teams_webhook_url, json=message, headers=headers)

if response.status_code == 200:
    print("Data sent successfully to Teams.")
else:
    print("Failed to send data to Teams.")
    print(response.text)
