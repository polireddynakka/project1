
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

# Prepare the adaptive card payload
adaptive_card = {
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
        },
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

# Prepare the message payload
message = {
    "type": "message",
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": adaptive_card
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
