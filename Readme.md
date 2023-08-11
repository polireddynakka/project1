def format_to_human_readable(data_list):
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
    formatted_block = ""
    for line in block:
        if not line.startswith('Account:') and not line.startswith('apm_id:'):
            formatted_block += line + " "
    return formatted_block



application_name: Carespre-Slvr Month: July  Cost_Usage_by_Service:     . AWS Key Management Service - Cost: $8.15   . AWS Lambda - Cost: $0.03   . Amazon Elastic Load Balancing - Cost: $33.48   . Amazon Simple Notification Service - Cost: $0.00   . Amazon Simple Storage Service - Cost: $0.05 Total cost: $41.71 

application_name: Carespre-Gold Month: July  Cost_Usage_by_Service:     . AWS Key Management Service - Cost: $8.15   . AWS Lambda - Cost: $2.61   . Amazon Elastic Load Balancing - Cost: $50.24   . Amazon Simple Storage Service - Cost: $0.18 Total cost: $61.18 

application_name: Carespre-Plat Month: July  Cost_Usage_by_Service:     . AWS Key Management Service - Cost: $8.18   . AWS Lambda - Cost: $123.36   . Amazon Elastic Load Balancing - Cost: $67.92   . Amazon Simple Storage Service - Cost: $0.53 Total cost: $199.99 

application_name: Carespre-DR Month: July  Cost_Usage_by_Service:     . AWS Key Management Service - Cost: $9.18   . AWS Lambda - Cost: $0.11   . Amazon Elastic Load Balancing - Cost: $33.48   . Amazon Simple Storage Service - Cost: $0.13 Total cost: $42.90 








