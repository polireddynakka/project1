import json
import boto3

input_data = [
    ['Account: 123456, 'apm_id: APM37676', 'application_name: first-slvr', 'Month: July ', 'Cost_Usage_by_Service: ', ' Total cost: $0.00', 'Account: 12345678, 'apm_id: APM37676', 'application_name: first-Gold', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Glue - Cost: $1.00', '  . AWS Key Management Service - Cost: $2.00', '  . AWS Lambda - Cost: $0.00', '  . AWS Secrets Manager - Cost: $0.00', '  . Amazon Athena - Cost: $0.01', '  . EC2 - Other - Cost: $136.45', '  . Amazon Elastic Compute Cloud - Compute - Cost: $5904.18', '  . Amazon OpenSearch Service - Cost: $22478.67', '  . Amazon Simple Storage Service - Cost: $133.06', '  . AmazonCloudWatch - Cost: $15.48', 'Total cost: $2156.92'],
    ['Account: 32456876564', 'apm_id: APM37676', 'application_name: first-Plat', 'Month: July ', 'Cost_Usage_by_Service: ', ' Total cost: $0.00', 'Account:134765765272', 'apm_id:APM37676', 'application_name: first-DR', 'Month: July ', 'Cost_Usage_by_Service: ', ' Total cost: $0.00', 'Account:4756q8764874', 'apm_id: APM3767542', 'application_name: Carespre-Slvr', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Key Management Service - Cost: $8.15', '  . AWS Lambda - Cost: $0.03', '  . Amazon Elastic Load Balancing - Cost: $33.48', '  . Amazon Simple Notification Service - Cost: $0.00', '  . Amazon Simple Storage Service - Cost: $0.05', 'Total cost: $41.71']
]

def format_to_human_readable(data_list):
    formatted_data = '\n'.join(data_list)
    return formatted_data

def format_to_outlook_supported(data_list):
    formatted_data = "\n".join(data_list)
    formatted_data = formatted_data.replace(". ", "\n  . ")
    return formatted_data

def send_email_to_outlook(formatted_data):
    sns = boto3.client('sns', region_name='your_region')  # Replace 'your_region' with your AWS region
    sns_topic_arn = 'your_sns_topic_arn'  # Replace 'your_sns_topic_arn' with your SNS topic ARN

    subject = "Formatted Data for Outlook"
    message = formatted_data

    sns.publish(
        TopicArn=sns_topic_arn,
        Subject=subject,
        Message=message
    )

if __name__ == "__main__":
    for data in input_data:
        human_readable = format_to_human_readable(data)
        outlook_supported = format_to_outlook_supported(data)
        
        send_email_to_outlook(outlook_supported)





