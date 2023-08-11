sdef format_to_human_readable(data_list):
    formatted_data = '\n'.join(data_list)
    return formatted_data

def format_to_outlook_supported(data_list):
    formatted_data = ""
    current_block = []
    
    for line in data_list:
        if line.startswith('apm_id:'):
            if current_block:
                formatted_block = "\n".join(current_block)
                formatted_data += formatted_block + "\n\n"
                current_block = []
        current_block.append(line)
    
    if current_block:
        formatted_block = "\n".join(current_block)
        formatted_data += formatted_block + "\n\n"

    return formatted_data

def send_email_to_outlook(subject, message):
    sns = boto3.client('sns', region_name='your_region')  # Replace 'your_region' with your AWS region
    sns_topic_arn = 'your_sns_topic_arn'  # Replace 'your_sns_topic_arn' with your SNS topic ARN

    sns.publish(
        TopicArn=sns_topic_arn,
        Subject=subject,
        Message=message
    )

def group_data_by_apm_id(input_data):
    grouped_data = {}
    current_apm_id = None
    current_group = []

    for data in input_data:
        for item in data:
            if item.startswith('apm_id:'):
                if current_apm_id and current_group:
                    if current_apm_id not in grouped_data:
                        grouped_data[current_apm_id] = []
                    grouped_data[current_apm_id].extend(current_group)
                current_apm_id = item.split(': ')[1]
                current_group = []
            current_group.append(item)

    if current_apm_id and current_group:
        if current_apm_id not in grouped_data:
            grouped_data[current_apm_id] = []
        grouped_data[current_apm_id].extend(current_group)

    return grouped_data

if __name__ == "__main__":
    grouped_data = group_data_by_apm_id(input_data)

    for apm_id, data in grouped_data.items():
        human_readable = format_to_human_readable(data)
        outlook_supported = format_to_outlook_supported(data)

        subject = f"Data for APM ID: {apm_id}"

        try:
            send_email_to_outlook(subject, outlook_supported)
            print(f"Email sent for APM ID: {apm_id}")
        except Exception as e:
            print(f"Error sending email for APM ID {apm_id}: {e}")




# format the email message with each apm_id block side by side, you can modify the format_to_outlook_supported function

def format_to_outlook_supported(data_list):
    formatted_data = ""
    apm_blocks = {}
    current_apm_id = None
    
    for line in data_list:
        if line.startswith('apm_id:'):
            if current_apm_id:
                formatted_block = format_block(apm_blocks[current_apm_id])
                formatted_data += formatted_block + "\n\n"
                apm_blocks[current_apm_id] = []
            current_apm_id = line.split(': ')[1]
            apm_blocks[current_apm_id] = []
        apm_blocks[current_apm_id].append(line)
    
    if current_apm_id:
        formatted_block = format_block(apm_blocks[current_apm_id])
        formatted_data += formatted_block + "\n\n"

    return formatted_data

def format_block(block):
    max_line_length = max(len(line) for line in block)
    formatted_block = ""
    
    num_lines = max(len(line.split('  ')) for line in block)
    
    for i in range(num_lines):
        for line in block:
            line_parts = line.split('  ')
            if len(line_parts) > i:
                formatted_line = line_parts[i].ljust(max_line_length)
            else:
                formatted_line = "".ljust(max_line_length)
            formatted_block += formatted_line + "     "
        formatted_block += "\n"
    
    return formatted_block



#HTMl Format
def format_to_html(data_list):
    html = "<html><body>"
    apm_blocks = {}
    current_apm_id = None
    
    for line in data_list:
        if line.startswith('apm_id:'):
            if current_apm_id:
                html += format_block_html(apm_blocks[current_apm_id]) + "<br><br>"
            current_apm_id = line.split(': ')[1]
            apm_blocks[current_apm_id] = []
        apm_blocks[current_apm_id].append(line)
    
    if current_apm_id:
        html += format_block_html(apm_blocks[current_apm_id]) + "<br><br>"
    
    html += "</body></html>"
    return html

def format_block_html(block):
    html = "<table><tr>"
    for line in block:
        parts = line.split(': ')
        html += f"<td>{parts[1]}</td>"
    html += "</tr></table>"
    return html


<html><body><table><tr><td>APM1006706</td><td>993514063544</td><td>Carespre-Slvr</td><td>July </td><td></td><td>$8.15</td><td>$0.03</td><td>$33.48</td><td>$0.00</td><td>$0.05</td><td>$41.71</td></tr></table><br><br><table><tr><td>APM1006706</td><td>339644262595</td><td>Carespre-Gold</td><td>July </td><td></td><td>$8.15</td><td>$2.61</td><td>$50.24</td><td>$0.18</td><td>$61.18</td></tr></table><br><br><table><tr><td>APM1006706</td><td>988647855354</td><td>Carespre-Plat</td><td>July </td><td></td><td>$8.18</td><td>$123.36</td><td>$67.92</td><td>$0.53</td><td>$199.99</td></tr></table><br><br><table><tr><td>APM1006706</td><td>988647855354</td><td>Carespre-DR</td><td>July </td><td></td><td>$9.18</td><td>$0.11</td><td>$33.48</td><td>$0.13</td><td>$42.90</td></tr></table><br><br></body></html>

import boto3
from botocore.exceptions import NoCredentialsError

# Your input data
input_data = [...]

def format_block_text(block):
    # Your formatting logic here
    formatted_block = ...
    return formatted_block

def send_email_to_sns(subject, message):
    sns = boto3.client('sns', region_name='your_region')  # Replace 'your_region' with your AWS region
    sns_topic_arn = 'your_sns_topic_arn'  # Replace with your SNS topic ARN

    try:
        sns.publish(
            TopicArn=sns_topic_arn,
            Subject=subject,
            Message=message,
        )
        print("Message sent successfully.")
    except NoCredentialsError:
        print("Credentials not available")

if __name__ == "__main__":
    formatted_blocks = [format_block_text(block) for block in input_data]
    formatted_data = '\n\n'.join(formatted_blocks)
    subject = "Formatted Data"
    
    try:
        send_email_to_sns(subject, formatted_data)



