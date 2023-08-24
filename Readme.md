
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

