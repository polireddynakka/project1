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

