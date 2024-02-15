
import boto3
import traceback
import os

def send_email_sns(subject, message):
    # Specify the SNS topic ARN
    sns_topic_arn = "your_sns_topic_arn"

    # Create an SNS client
    sns = boto3.client("sns")

    try:
        # Publish the message to the specified SNS topic
        response = sns.publish(
            TopicArn=sns_topic_arn,
            Subject=subject,
            Message=message
        )
        print("Email notification sent! Message ID:", response['MessageId'])
    except Exception as e:
        print("Error sending email notification:", e)
        traceback.print_exc()

def lambda_handler(event, context):
    try:
        # Your Python Lambda code here

        # Simulate an error
        raise Exception("This is a sample error message.")
    except Exception as e:
        # Send an email notification with the error message
        send_email_sns("Lambda Error", str(e))
        return {
            "statusCode": 500,
            "body": "Error occurred. Email notification sent with error details.",
        }
def format_blocks(data):
    blocks = data.strip().split('\n\n')
    block_lines = [block.split('\n') for block in blocks]
    
    max_lines = max(len(block) for block in block_lines)
    max_column_width = 40
    
    formatted_output = ''
    
    for line_index in range(max_lines):
        for block in block_lines:
            if line_index < len(block):
                formatted_output += block[line_index].ljust(max_column_width) + ' | '
            else:
                formatted_output += ' ' * max_column_width + ' | '
        formatted_output += '\n'
    
    return formatted_output

input_data = """
[Your provided input data here]
"""

formatted_output = format_blocks(input_data)
print(formatted_output)






import requests
from requests.auth import HTTPBasicAuth

def update_confluence_page(page_id, title, content):
    # Set your Confluence configuration
    confluence_url = "<CONFLUENCE_URL>"
    username = "<USERNAME>"
    password = "<PASSWORD>"
    
    # Define the API endpoint URL for updating the page
    url = f"{confluence_url}/rest/api/content/{page_id}"
    
    # Create the JSON payload for updating the page
    payload = {
        "version": {
            "number": 2,
        },
        "type": "page",
        "title": title,
        "body": {
            "storage": {
                "value": content,
                "representation": "storage"
            }
        }
    }
    
    # Set headers for authentication and content type
    headers = {
        "Content-Type": "application/json"
    }
    
    # Make the PUT request to update the page
    response = requests.put(url, json=payload, auth=HTTPBasicAuth(username, password), headers=headers)
    
    return response

def create_table(data):
    table_markup = "| Service | Cost |\n| --- | --- |\n"
    for service, cost in data.items():
        table_markup += f"| {service} | {cost} |\n"
    return table_markup

def lambda_handler(event, context):
    # Content to be updated on the page
    title = "Updated Page Title"  # Change this to your desired title
    data = {
        "AWS Key Management Service": "$8.15",
        "AWS Lambda": "$0.03",
        "Amazon Elastic Load Balancing": "$33.48",
        "Amazon Simple Notification Service": "$0.00",
        "Amazon Simple Storage Service": "$0.05",
        "Total cost": "$41.71"
    }

    # Create tabular content
    table_content = create_table(data)

    # Update the Confluence page
    response = update_confluence_page("<PAGE_ID>", title, table_content)

    # Return response
    return {
        "statusCode": response.status_code,
        "body": response.text
    }







import requests
from requests.auth import HTTPBasicAuth

def update_confluence_page(page_id, title, content):
    # Set your Confluence configuration
    confluence_url = "<CONFLUENCE_URL>"
    username = "<USERNAME>"
    password = "<PASSWORD>"
    
    # Define the API endpoint URL for updating the page
    url = f"{confluence_url}/rest/api/content/{page_id}"
    
    # Create the JSON payload for updating the page
    payload = {
        "version": {
            "number": 2,
        },
        "type": "page",
        "title": title,
        "body": {
            "storage": {
                "value": content,
                "representation": "storage"
            }
        }
    }
    
    # Set headers for authentication and content type
    headers = {
        "Content-Type": "application/json"
    }
    
    # Make the PUT request to update the page
    response = requests.put(url, json=payload, auth=HTTPBasicAuth(username, password), headers=headers)
    
    return response

def create_table(data):
    table_markup = "| Service |"
    header_row = "| --- |"

    # Create the header row and content rows for each account
    for account, services in data.items():
        table_markup += f" {account} |"
        header_row += " --- |"

    table_markup += "\n" + header_row + "\n"

    # Iterate over services to populate each account's column
    for service, costs_per_account in services.items():
        row = f"| {service} |"
        for account, cost in costs_per_account.items():
            row += f" {cost} |"
        table_markup += row + "\n"

    return table_markup

def lambda_handler(event, context):
    # Content to be updated on the page
    title = "Updated Page Title"  # Change this to your desired title
    data = {
        "Account 1": {
            "AWS Key Management Service": "$8.15",
            "AWS Lambda": "$0.03",
            "Amazon Elastic Load Balancing": "$33.48"
        },
        "Account 2": {
            "AWS Key Management Service": "$6.20",
            "AWS Lambda": "$0.05",
            "Amazon Elastic Load Balancing": "$28.75"
        },
        # Add more accounts and services as needed
    }

    # Create tabular content
    table_content = create_table(data)

    # Update the Confluence page
    response = update_confluence_page("<PAGE_ID>", title, table_content)

    # Return response
    return {
        "statusCode": response.status_code,
        "body": response.text
    }

import boto3
import openpyxl
from datetime import datetime, timedelta

def lambda_handler(event, context):
    # Initialize AWS Cost Explorer client
    cost_explorer = boto3.client('ce')

    # Set the time range for cost and usage data
    start = (datetime.now() - timedelta(days=30)).strftime('%Y-%m-%d')
    end = datetime.now().strftime('%Y-%m-%d')

    # Retrieve cost and usage data
    response = cost_explorer.get_cost_and_usage(
        TimePeriod={'Start': start, 'End': end},
        Granularity='DAILY',
        Metrics=['BlendedCost']
    )

    # Create an Excel workbook
    workbook = openpyxl.Workbook()
    sheet = workbook.active
    sheet.title = "CostData"

    # Write headers
    headers = ['TimePeriod', 'BlendedCost']
    for col_idx, header in enumerate(headers, start=1):
        sheet.cell(row=1, column=col_idx, value=header)

    # Write cost and usage data
    data = response['ResultsByTime']
    for row_idx, entry in enumerate(data, start=2):
        sheet.cell(row=row_idx, column=1, value=entry['TimePeriod']['Start'])
        sheet.cell(row=row_idx, column=2, value=entry['Total']['BlendedCost']['Amount'])

    # Save Excel file
    excel_file_path = '/tmp/cost_report.xlsx'
    workbook.save(excel_file_path)

    # Upload Excel file to S3
    s3 = boto3.client('s3')
    s3.upload_file(excel_file_path, '<YOUR_S3_BUCKET>', 'cost_report.xlsx')

    return {
        'statusCode': 200,
        'body': 'Cost and usage data saved to Excel file'
    }





import requests

def lambda_handler(event, context):
    # Replace with your actual Confluence URL
    confluence_url = "https://your-confluence-instance.com/rest/api/content"

    # Make a GET request to the Confluence API
    response = requests.get(confluence_url)

    if response.status_code == 200:
        return {
            "statusCode": 200,
            "body": "Successfully fetched data from Confluence API"
        }
    else:
        return {
            "statusCode": response.status_code,
            "body": "Failed to fetch data from Confluence API"
        }








from colorama import Fore, Style

input_data = """
... (your input data here) ...
"""

def format_data(data):
    lines = data.strip().split("\n\n")
    formatted_data = []

    for line in lines:
        formatted_line = []
        for item in line.split("\n"):
            key, value = item.split(": ", 1)
            formatted_line.append(f"{Fore.CYAN}{key}:{Style.RESET_ALL} {value}")

        formatted_data.append("\n".join(formatted_line))

    return formatted_data

def group_and_print_blocks(data):
    blocks = data.strip().split("\n\napm_id: ")
    for block in blocks[1:]:  # Skip the first empty block
        apm_id, block_data = block.split("\n", 1)
        print(f"apm_id: {apm_id}\n{block_data}\n")

formatted_data = format_data(input_data)
group_and_print_blocks("\n\n".join(formatted_data))




import json
import csv

# JSON data you've provided
json_data = """
{
    "GroupDefinitions": [
        {
            "Type": "DIMENSION",
            "Key": "SERVICE"
        }
    ],
    "ResultsByTime": [
        {
            "TimePeriod": {
                "Start": "2023-07-01",
                "End": "2023-08-01"
            },
            "Total": {},
            "Groups": [
                {
                    "Keys": ["AWS Key Management Service"],
                    "Metrics": {
                        "UnblendedCost": {
                            "Amount": "2.038903796",
                            "Unit": "USD"
                        }
                    }
                },
                {
                    "Keys": ["Amazon Elastic Load Balancing"],
                    "Metrics": {
                        "UnblendedCost": {
                            "Amount": "117.18",
                            "Unit": "USD"
                        }
                    }
                },
                {
                    "Keys": ["Amazon Simple Notification Service"],
                    "Metrics": {
                        "UnblendedCost": {
                            "Amount": "0.001872",
                            "Unit": "USD"
                        }
                    }
                },
                {
                    "Keys": ["Amazon Simple Storage Service"],
                    "Metrics": {
                        "UnblendedCost": {
                            "Amount": "0.4556783425",
                            "Unit": "USD"
                        }
                    }
                }
            ]
        }
    ],
    "Estimated": false
}
"""

# Parse the JSON data
data = json.loads(json_data)

# Extract relevant data
time_period = data['ResultsByTime'][0]['TimePeriod']
groups = data['ResultsByTime'][0]['Groups']

# Prepare data for CSV
csv_data = []
for group in groups:
    keys = ', '.join(group['Keys'])
    cost_amount = group['Metrics']['UnblendedCost']['Amount']
    cost_unit = group['Metrics']['UnblendedCost']['Unit']
    csv_data.append([time_period['Start'], time_period['End'], keys, cost_amount, cost_unit])

# Save data to CSV file
csv_filename = "cost_data.csv"
with open(csv_filename, 'w', newline='') as csv_file:
    csv_writer = csv.writer(csv_file)
    csv_writer.writerow(["Start Time", "End Time", "Keys", "Cost Amount", "Cost Unit"])
    csv_writer.writerows(csv_data)

print(f"Data saved to {csv_filename}")



def create_table(data):
    table_markup = "| Service |"
    header_row = "| --- |"

    # Create the header row and content rows for each account
    for account, services in data.items():
        table_markup += f" {account} |"
        header_row += " --- |"

    table_markup += "\n" + header_row + "\n"

    # Iterate over services to populate each account's column
    services_list = list(next(iter(data.values())).keys())
    for service in services_list:
      row = f"| {service} |"
      for account, account_data in data.items():
         cost = account_data.get(service, "-")
         row += f" {cost} |"
      table_markup += row + "\n"
    return table_markup



| Service | Slvr | Gold |
| --- | --- | --- |
| Provider Finder | $23.23 | $18.48 |


 
 
 
Key	Assignee	Summary	Created	Status	IT Team
ABCD1	Sham	DEV	8/14/2023 2:32	In Progress	Team1
ABCD2	Chow	Latest changes are not reflecting in SIT environment.	8/7/2023 5:46	In Progress	Team2
 
![image](https://github.com/polireddynakka/project1/assets/22856793/328c3407-55eb-4258-9a35-6e0f680ab574)

 
Reference links:

<!DOCTYPE html>
<html>
<head>
<style>
  table {
    border-collapse: collapse;
    width: 100%;
  }
  th, td {
    border: 1px solid black;
    padding: 8px;
    text-align: left;
  }
</style>
</head>
<body>

<h2>Tabular Data</h2>
<table>
  <tr>
    <th>Service</th>
    <th>Slvr</th>
    <th>Gold</th>
  </tr>
  <tr>
    <td>Provider Finder</td>
    <td>$23.23</td>
    <td>$18.48</td>
  </tr>
</table>

</body>
</html>




html_content = """
<html>
<head></head>
<body>
<h2>Tabular Data</h2>
<table>
  <tr>
    <th>Header 1</th>
    <th>Header 2</th>
  </tr>
  <tr>
    <td>Data 1</td>
    <td>Data 2</td>
  </tr>
</table>
</body>
</html>

Service              Slvr      Gold
-----------------------------------
Provider Finder      $23.23    $18.48






data = {
    "Slvr": {
        "Provider Finder": "$23.23"
    },
    "Gold": {
        "Provider Finder": "$18.48"
    }
}

# Determine the width of each column
service_width = max(len("Service"), max(len(service) for service in data.values()))
cost_width = max(len("Slvr"), len("Gold"))

# Generate the header row
header_row = f"{'Service':<{service_width}} {'Slvr':<{cost_width}} {'Gold':<{cost_width}}\n"
separator = "-" * (service_width + cost_width * 2 + 2) + "\n"

# Generate the content rows
content_rows = ""
for service, costs in data.items():
    content_rows += f"{service:<{service_width}} "
    content_rows += f"{costs.get('Slvr', ''):<{cost_width}} "
    content_rows += f"{costs.get('Gold', ''):<{cost_width}}\n"

# Concatenate the header row, separator, and content rows
plain_text_table = header_row + separator + content_rows

print(plain_text_table)








import json
import requests

def lambda_handler(event, context):
    teams_webhook_url = "YOUR_TEAMS_WEBHOOK_URL"
    message = {
        "text": "Hello from Lambda! This is a notification for your team."
    }
    
    headers = {
        "Content-Type": "application/json"
    }
    
    response = requests.post(teams_webhook_url, data=json.dumps(message), headers=headers)
    
    if response.status_code == 200:
        return {
            "statusCode": 200,
            "body": "Message sent to Teams successfully"
        }
    else:
        return {
            "statusCode": response.status_code,
            "body": "Failed to send message to Teams"
        }

# Sample input data
input_data = []

# Process input data and generate Markdown table
markdown_table = "**APM Data**\n\n"
markdown_table += "| apm_id | Account | application_name | Month | Cost_Usage_by_Service | Total cost |\n"
markdown_table += "|--------|---------|------------------|-------|-----------------------|------------|\n"

for block in input_data:
    lines = block.split("\n")
    apm_id = lines[0].split(": ")[1].strip()
    account = lines[1].split(": ")[1].strip()
    app_name = lines[2].split(": ")[1].strip()
    month = lines[3].split(": ")[1].strip()
    cost_lines = [line.strip() for line in lines[5:] if line.strip()]
    cost_info = "<br>".join(cost_lines)
    total_cost = lines[-1].split(": ")[1].strip()

    markdown_table += f"| {apm_id} | {account} | {app_name} | {month} | {cost_info} | {total_cost} |\n"

# Print the generated Markdown table
print(markdown_table)






input_data = """
# Paste your input data here
"""

output_data = []
entry_lines = input_data.strip().split("apm_id: ")

for entry in entry_lines[1:]:
    lines = entry.strip().split("\n")
    entry_dict = {"apm_id": lines[0].split()[0], "Account": lines[1].split()[1]}
    
    for line in lines[2:]:
        if line.startswith("application_name:"):
            entry_dict["application_name"] = line.split(": ")[1]
        elif line.startswith("Month:"):
            entry_dict["Month"] = line.split(": ")[1]
        elif line.startswith("Cost_Usage_by_Service:"):
            entry_dict["Cost_Usage_by_Service"] = []
        elif line.startswith("Total cost:"):
            entry_dict["Total_cost"] = line.split(": ")[1]
        elif line.startswith(". "):
            entry_dict["Cost_Usage_by_Service"].append(line[2:])
    
    output_data.append(entry_dict)

print(output_data)




markdown_table = "**APM Data**\n\n"
markdown_table += "| apm_id | Account | application_name | Month | Cost_Usage_by_Service | Total cost |\n"
markdown_table += "|--------|---------|------------------|-------|-----------------------|------------|\n"

for entry in input_data:
    apm_id = entry["apm_id"]
    account = entry["Account"]
    app_name = entry["application_name"]
    month = entry["Month"]
    cost_info = "<br>".join(entry["Cost_Usage_by_Service"])
    total_cost = entry["Total_cost"]

    markdown_table += f"| {apm_id} | {account} | {app_name} | {month} | {cost_info} | {total_cost} |\n"

# Print or use the generated Markdown table
print(markdown_table)



import boto3
import datetime

# Set up the default session using a named profile
aws_profile_name = 'YOUR_PROFILE_NAME'
boto3.setup_default_session(profile_name=aws_profile_name)

# Create an EC2 client
ec2 = boto3.client('ec2')

# Specify the snapshot description you want to search for
snapshot_description = 'YOUR_SNAPSHOT_DESCRIPTION'

# Initialize total cost
total_cost = 0.0

# Get a list of volumes
volumes = ec2.describe_volumes()

# Get the current time
current_time = datetime.datetime.now(datetime.timezone.utc)

# Iterate through the volumes
for volume in volumes['Volumes']:
    # Check if the volume has a snapshot with the specified description
    snapshot_found = False
    snapshot_id = None

    for attachment in volume['Attachments']:
        if 'SnapshotId' in attachment:
            snapshot_id = attachment['SnapshotId']
            snapshot_response = ec2.describe_snapshots(SnapshotIds=[snapshot_id])

            for snapshot in snapshot_response['Snapshots']:
                if 'Description' in snapshot and snapshot['Description'] == snapshot_description:
                    snapshot_found = True
                    break

    if snapshot_found:
        # Calculate the cost based on the snapshot age
        snapshot = snapshot_response['Snapshots'][0]
        start_time = snapshot['StartTime']
        snapshot_age_hours = (current_time - start_time).total_seconds() / 3600
        snapshot_cost = snapshot_age_hours * 0.05  # Adjust the cost per hour as needed

        # Add the cost of the snapshot to the total
        total_cost += snapshot_cost

        # Print information about the snapshot and its cost
        print(f"Snapshot ID: {snapshot_id}")
        print(f"Snapshot Age (hours): {snapshot_age_hours}")
        print(f"Snapshot Cost: ${snapshot_cost:.2f}\n")

# Print the total cost for all snapshots with the specified description
print(f"Total Cost for Snapshots with Description '{snapshot_description}': ${total_cost:.2f}")







arn:aws:ec2:us-east-1:509230727284:snapshot/snap-07b421d9c35564abc



import pandas as pd

# Load the Excel file
file_path = 'snapshot_data.xlsx'
df = pd.read_excel(file_path)

# Define a function to get snapshot descriptions
def get_snapshot_description(snapshot):
    if snapshot % 2 == 0:
        return 'Even Snapshot'
    else:
        return 'Odd Snapshot'

# Add a 'Snapshot Description' column to the DataFrame
df['Snapshot Description'] = df['Snapshot'].apply(get_snapshot_description)

# Save the updated DataFrame back to the Excel file
df.to_excel(file_path, index=False)

print("Excel file updated successfully.")




import pandas as pd
import boto3

# Replace these with your AWS credentials
aws_access_key = 'YOUR_AWS_ACCESS_KEY'
aws_secret_key = 'YOUR_AWS_SECRET_KEY'
aws_region = 'us-east-1'  # Update with your AWS region

# Create a function to get snapshot descriptions
def get_snapshot_description(snapshot_arn):
    session = boto3.Session(
        aws_access_key_id=aws_access_key,
        aws_secret_access_key=aws_secret_key,
        region_name=aws_region
    )
    ec2 = session.client('ec2')
    
    # Extract the snapshot ID from the ARN
    snapshot_id = snapshot_arn.split('/')[-1]
    
    try:
        response = ec2.describe_snapshots(SnapshotIds=[snapshot_id])
        if 'Snapshots' in response and len(response['Snapshots']) > 0:
            return response['Snapshots'][0]['Description']
        else:
            return 'Snapshot not found'
    except Exception as e:
        return str(e)

# Load your Excel file
excel_file = pd.ExcelFile('your_excel_file.xlsx')

# Specify the sheet name where your data is
sheet_name = 'YourSheetName'

# Read the data from the Excel sheet
df = excel_file.parse(sheet_name)

# Create a new column for Snapshot Description
df['Snapshot Description'] = df['Snapshot ARN'].apply(get_snapshot_description)

# Save the data to a new Excel file
output_file = 'test_file_with_snapshot_description.xlsx'
df.to_excel(output_file, index=False, engine='openpyxl')

print(f"Data with Snapshot Descriptions saved to {output_file}")




arn:aws:ec2:us-east-1:644490409399:snapshot/snap-0e30db85672093ae0







import openpyxl

# Define the source and destination Excel files
source_file = 'source_file.xlsx'
destination_file = 'destination_file.xlsx'

# Open the source Excel file
wb = openpyxl.load_workbook(source_file)

# Get the worksheet
ws = wb.active

# Define the column numbers for the snapshot description and cost columns
snapshot_description_column_number = 3
cost_column_number = 4

# Get the snapshot description to match with
snapshot_description_to_match = 'snapshot_description'

# Create a list to store the rows that match the snapshot description
matching_rows = []

# Iterate over the rows in the worksheet
for row in ws.rows:

    # Get the snapshot description for the current row
    snapshot_description = row[snapshot_description_column_number].value

    # Get the cost for the current row
    cost = row[cost_column_number].value

    # Check if the snapshot description matches the snapshot description to match
    if snapshot_description == snapshot_description_to_match:

        # Add the current row to the matching rows list
        matching_rows.append(row)

# Create a new Excel file to store the matching rows
destination_wb = openpyxl.Workbook()

# Create a worksheet in the destination Excel file
destination_ws = destination_wb.active

# Copy the header row from the source worksheet to the destination worksheet
destination_ws.append(ws.rows[0])

# Copy the matching rows from the source worksheet to the destination worksheet
for row in matching_rows:
    destination_ws.append(row)

# Save the destination Excel file
destination_wb.save(destination_file)

# Close the source and destination Excel files
wb.close()
destination_wb.close()





import openpyxl
import csv

# Define the source and destination Excel files
source_file = 'source_file.xlsx'
destination_file = 'destination_file.csv'

# Open the source Excel file
wb = openpyxl.load_workbook(source_file)

# Get the worksheet
ws = wb.active

# Define the column numbers for the snapshot description and cost columns
snapshot_description_column_number = 3
cost_column_number = 4

# Get the snapshot description to match with
snapshot_description_to_match = 'snapshot_description'

# Create a CSV file to store the matching rows
with open(destination_file, 'w', newline='') as f:
    writer = csv.writer(f)

    # Write the header row
    writer.writerow(['Snapshot Description', 'Cost'])

    # Iterate over the rows in the worksheet
    for row in ws.rows:

        # Get the snapshot description for the current row
        snapshot_description = row[snapshot_description_column_number].value

        # Get the cost for the current row
        cost = row[cost_column_number].value

        # Check if the snapshot description matches the snapshot description to match
        if snapshot_description == snapshot_description_to_match:

            # Write the current row to the CSV file
            writer.writerow([snapshot_description, cost])

# Close the source and destination files
wb.close()
f.close()





# Create a CSV file to store the matching rows
with open(destination_file, 'w', newline='') as f:
    writer = csv.writer(f)

    # Write the header row
    writer.writerow(['Snapshot Description', 'Cost'])

    # Iterate over the costs list
    for cost in costs:

        # Write the current cost to the CSV file
        writer.writerow([snapshot_description_to_match, cost])

    # Write the total cost to the last row of the CSV file
    writer.writerow(['Total Cost', total_cost]




import openpyxl
import csv

# Define the source and destination Excel files
source_file = 'source_file.xlsx'
destination_file = 'destination_file.csv'

# Open the source Excel file
wb = openpyxl.load_workbook(source_file)

# Get the worksheet
ws = wb.active

# Define the column numbers for the snapshot description, cost, and additional columns
snapshot_description_column_number = 3
cost_column_number = 4
additional_column_numbers = [5, 6]

# Get the snapshot description to match with
snapshot_description_to_match = 'snapshot_description'

# Create a list to store the additional columns
additional_columns = []

# Iterate over the rows in the worksheet
for row in ws.rows:

    # Get the snapshot description for the current row
    snapshot_description = row[snapshot_description_column_number].value

    # Get the cost for the current row
    cost = row[cost_column_number].value

    # Get the additional columns for the current row
    additional_column_values = []
    for column_number in additional_column_numbers:
        additional_column_values.append(row[column_number].value)

    # Check if the snapshot description matches the snapshot description to match
    if snapshot_description == snapshot_description_to_match:

        # Add the additional columns to the list
        additional_columns.append(additional_column_values)

# Create a CSV file to store the matching rows
with open(destination_file, 'w', newline='') as f:
    writer = csv.writer(f)

    # Write the header row
    writer.writerow(['Snapshot Description', 'Cost', 'Additional Column 1', 'Additional Column 2'])


    # Iterate over the costs and additional columns list
    for cost, additional_column_values in zip(costs, additional_columns):

        # Write the current cost and additional columns to the CSV file
        row = [snapshot_description_to_match, cost] + additional_column_values
        writer.writerow(row)

# Close the source and destination files
wb.close()
f.close()





latest

import boto3
import traceback

def send_email(subject, body):
    # Specify the sender and recipient email addresses
    sender_email = "your_verified_email@example.com"
    recipient_email = "recipient_email@example.com"

    # Specify the AWS Region
    aws_region = "your_aws_region"

    # Create a new SES resource
    ses = boto3.client("ses", region_name=aws_region)

    # Create the message body
    message_body = {
        "Subject": {"Data": subject},
        "Body": {"Text": {"Data": body}},
    }

    try:
        # Send the email
        response = ses.send_email(
            Source=sender_email,
            Destination={"ToAddresses": [recipient_email]},
            Message=message_body,
        )
        print("Email sent! Message ID:", response["MessageId"])
    except Exception as e:
        print("Error sending email:", e)
        traceback.print_exc()

def lambda_handler(event, context):
    try:
        # Your Python Lambda code here

        # Simulate an error
        raise Exception("This is a sample error message.")
    except Exception as e:
        # Send an email with the error message
        send_email("Lambda Error", str(e))
        return {
            "statusCode": 500,
            "body": "Error occurred. Email sent with error details.",
        }




today = datetime.date.today()
            if today.day == 16:
                start_date = datetime.date(today.year, today.month, 1)
                end_date = datetime.date(today.year, today.month, 15)
            elif:
                start_date = datetime.date(today.year, today.month - 1, 1)
                end_date = datetime.date(today.year, today.month, 1)


import datetime

today = datetime.date.today()

# Calculate the number of days until next Monday
days_until_next_monday = (0 - today.weekday() + 7) % 7

# Calculate the next Monday's date
next_monday = today + datetime.timedelta(days=days_until_next_monday)

# Set the time for Monday morning (adjust as needed)
monday_morning = datetime.datetime.combine(next_monday, datetime.time(8, 0))

# Print or use 'monday_morning' in your code to schedule the task
print("Next Monday morning:", monday_morning)











today = datetime.date.today()
            start_date = None
            end_date = None
            if today.day == 16:
                start_date = datetime.date(today.year, today.month, 1)
                end_date = datetime.date(today.year, today.month, 15)
            elif today.weekday() == 0:
                start_date = today - datetime.timedelta(days=today.weekday() + 7)
                end_date = start_date.strftime('%Y-%m-%d') + datetime.timedelta(days=6)
            else:
                start_date = datetime.date(today.year, today.month - 1, 1)
                end_date = datetime.date(today.year, today.month, 1)


            # Set the time period

            time_period={
                    'Start': start_date.strftime('%Y-%m-%d'),
                    'End': end_date.strftime('%Y-%m-%d')
            }





template(name="dynamicTemplate" type="list") {
    property(name="msg" position.from="1" position.to="32")
}

if $msg contains 'your_key_value' then {
    action(
        type="omfile"
        file="/usr/docker/test2.log"
        template="dynamicTemplate"
    )
} else if $msg contains 'another_key_value' then {
    action(
        type="omfile"
        file="/usr/docker/test3.log"
        template="dynamicTemplate"
    )
} else {
    action(
        type="omfile"
        file="/usr/docker/test.log"
        template="dynamicTemplate"
    )
}



spec:
  minReplicas: 30
  maxReplicas: 75
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Pods
        value: 1
        periodSeconds: 300
  metrics:
  - type: Pods
    pods:
      metric:
        name: istio_requests_per_second
      target:
        type: AverageValue
        averageValue: 10
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: test



Split Panes:
To split the terminal vertically, use the shortcut Cmd + D.
To split the terminal horizontally, use the shortcut Cmd + Shift + D.
Custom Themes:
Navigate to iTerm2 > Preferences > Profiles > Colors.
Choose a built-in color scheme or import a custom theme file.
You can find custom themes online or create your own using the iTerm2 color presets.
Shell Integration:
Follow the instructions provided in the iTerm2 documentation to enable shell integration for your shell (e.g., Bash, Zsh).
This typically involves running a script provided by iTerm2 to update your shell configuration file (e.g., .bashrc, .zshrc).
Hotkey Window:
Navigate to iTerm2 > Preferences > Keys.
Set a hotkey combination for the "Show/hide iTerm2 with a system-wide hotkey" action.
Once configured, use the assigned hotkey to toggle the iTerm2 hotkey window.
Advanced Search:
Use the shortcut Cmd + F to open the search bar.
Enter your search query and press Enter to search through the terminal output.
Use regex patterns for more advanced search queries.
Trigger Commands:
Navigate to iTerm2 > Preferences > Profiles > Advanced > Triggers.
Add a new trigger with a regular expression pattern and specify the action to execute when the pattern is matched.
For example, you can set up a trigger to highlight specific keywords or automatically run a command when certain output is detected.
Mouse Reporting:
Navigate to iTerm2 > Preferences > Profiles > Terminal.
Enable "Enable mouse reporting" to allow mouse interaction in the terminal.
You can now use the mouse to select text, resize panes, and click hyperlinks.
Paste History:
Use the shortcut Cmd + Shift + H to open the paste history.
Select a previously copied item from the list to paste it into the terminal.
Dynamic Profiles:
Navigate to iTerm2 > Preferences > Profiles.
Click on "Edit Profiles" and select "Dynamic Profiles."
Create new dynamic profiles based on criteria like hostname, user, or environment variables.
Session Management:
Enable session logging in iTerm2 > Preferences > Profiles > Session.
Use the tabs and window management features to organize your terminal sessions effectively.
Tmux Integration:
Install Tmux if you haven't already (brew install tmux on macOS).
Start a new Tmux session by running tmux in the terminal.
You can now manage Tmux sessions, windows, and panes directly within iTerm2.
Scripting Support:
Write scripts using AppleScript, JavaScript, or Python to automate tasks or extend iTerm2's functionality.
Refer to the iTerm2 documentation for examples and API references for scripting.
Touch Bar Support:
Customize the Touch Bar by navigating to iTerm2 > Preferences > Keys > Touch Bar.
Add commands, shortcuts, and controls to the Touch Bar based on your preferences.
These examples should help you get started with configuring and using various features in iTerm2. Experiment with different settings and configurations to tailor iTerm2 to your specific workflow and preferences.




                
            
