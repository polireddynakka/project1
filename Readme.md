apm_id: APM1003015
Account: 993514063544
application_name: Provider-Finder-Slvr
Month: July 
Cost_Usage_by_Service: 
Total cost: $0.00
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
apm_id: APM1003015
Account: 988647855354
application_name: Provider-Finder-Plat
Month: July 
Cost_Usage_by_Service: 
Total cost: $0.00
apm_id: APM1003015
Account: 988647855354
application_name: Provider-Finder-DR
Month: July 
Cost_Usage_by_Service: 
Total cost: $0.00
apm_id: APM1006706
Account: 993514063544
application_name: Carespre-Slvr
Month: July 
Cost_Usage_by_Service: 
. AWS Key Management Service - Cost: $8.15
. AWS Lambda - Cost: $0.03
. Amazon Elastic Load Balancing - Cost: $33.48
. Amazon Simple Notification Service - Cost: $0.00
. Amazon Simple Storage Service - Cost: $0.05
Total cost: $41.71
apm_id: APM1006706
Account: 339644262595
application_name: Carespre-Gold
Month: July 
Cost_Usage_by_Service: 
. AWS Key Management Service - Cost: $8.15
. AWS Lambda - Cost: $2.61
. Amazon Elastic Load Balancing - Cost: $50.24
. Amazon Simple Storage Service - Cost: $0.18
Total cost: $61.18
apm_id: APM1006706
Account: 988647855354
application_name: Carespre-Plat
Month: July 
Cost_Usage_by_Service: 
. AWS Key Management Service - Cost: $8.18
. AWS Lambda - Cost: $123.36
. Amazon Elastic Load Balancing - Cost: $67.92
. Amazon Simple Storage Service - Cost: $0.53
Total cost: $199.99

with this data please give python script using any python frameworks to format this data and send it to teams chat with a certain tabular format for human understanding 


import requests

# Replace with your actual Microsoft Teams webhook URL
teams_webhook_url = "YOUR_TEAMS_WEBHOOK_URL"

# Sample input data (replace this with your actual data)
input_data = """
# Input data here
"""

def parse_input_data(input_data):
    data_entries = []
    lines = input_data.strip().split('\n')
    entry = {}
    inside_costs = False
    cost_usage = {}

    for line in lines:
        if not line:  # Skip empty lines
            continue

        if line.startswith("#"):
            continue  # Skip comments

        if inside_costs:
            if line.startswith(". "):
                parts = line[2:].split(' - Cost: ')
                cost_usage[parts[0]] = parts[1]
            else:
                entry["Cost_Usage_by_Service"] = cost_usage
                cost_usage = {}
                inside_costs = False

        if ": " in line:
            key, value = line.split(": ", 1)
            if key == "Cost_Usage_by_Service":
                inside_costs = True
            else:
                entry[key] = value

    data_entries.append(entry)

    return data_entries

data = parse_input_data(input_data)

# Send formatted data to Teams
for entry in data:
    message = (
        f"apm_id: {entry['apm_id']}\n"
        f"Account: {entry['Account']}\n"
        f"application_name: {entry['application_name']}\n"
        f"Month: {entry['Month']}\n"
        f"Cost_Usage_by_Service:\n"
    )
    if 'Cost_Usage_by_Service' in entry:
        costs = entry['Cost_Usage_by_Service']
        for cost_key, cost_value in costs.items():
            message += f". {cost_key} - Cost: {cost_value}\n"
    else:
        message += "No cost breakdown available\n"
    
    message += f"Total cost: {entry.get('Total cost', 'N/A')}\n"

    # Send message to Teams
    payload = {
        "@type": "MessageCard",
        "@context": "http://schema.org/extensions",
        "themeColor": "0072C6",
        "summary": "Cost Data Summary",
        "sections": [
            {
                "activityTitle": "Cost Data Summary",
                "activitySubtitle": f"Month: {entry['Month']}",
                "markdown": True,
                "text": f"```\n{message}\n```"
            }
        ]
    }

    headers = {
        "Content-Type": "application/json"
    }

    response = requests.post(teams_webhook_url, json=payload, headers=headers)

    if response.status_code == 200:
        print("Data sent successfully to Teams.")
    else:
        print("Failed to send data to Teams.")
        print(response.text)

