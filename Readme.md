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





https://www.google.com/search?client=safari&rls=en&q=An+errro+occured%3A+HTTPSConnectionPool(host%3D%27confluence.elevancehealth.com%27%2C+port%3D443)%3A+Max+retries+exceeded+with+url%3A+%2Frest%2Fapi%2Fcontent%2F1175265086+(Caused+by+NewConnectionError(%27%3Curllib3.connection.HTTPSConnection+object+at+0x7f5370dbf940%3E%3A+Failed+to+establish+a+new+connection%3A+%5BErrno+-2%5D+Name+or+service+not+known%27))%22&ie=UTF-8&oe=UTF-8
