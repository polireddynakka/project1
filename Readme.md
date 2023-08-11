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





['Account: 993514063544', 'apm_id: APM1003015', 'application_name: Provider-Finder-Slvr', 'Month: July ', 'Cost_Usage_by_Service: ', ' Total cost: $0.00', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 339644262595', 'apm_id: APM1003015', 'application_name: Provider-Finder-Gold', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Glue - Cost: $471.65', '  . AWS Key Management Service - Cost: $17.42', '  . AWS Lambda - Cost: $0.00', '  . AWS Secrets Manager - Cost: $0.00', '  . Amazon Athena - Cost: $0.01', '  . EC2 - Other - Cost: $136.45', '  . Amazon Elastic Compute Cloud - Compute - Cost: $5904.18', '  . Amazon OpenSearch Service - Cost: $22478.67', '  . Amazon Simple Storage Service - Cost: $133.06', '  . AmazonCloudWatch - Cost: $15.48', 'Total cost: $29156.92', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 988647855354', 'apm_id: APM1003015', 'application_name: Provider-Finder-Plat', 'Month: July ', 'Cost_Usage_by_Service: ', ' Total cost: $0.00', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 988647855354', 'apm_id: APM1003015', 'application_name: Provider-Finder-DR', 'Month: July ', 'Cost_Usage_by_Service: ', ' Total cost: $0.00', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 993514063544', 'apm_id: APM1006706', 'application_name: Carespre-Slvr', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Key Management Service - Cost: $8.15', '  . AWS Lambda - Cost: $0.03', '  . Amazon Elastic Load Balancing - Cost: $33.48', '  . Amazon Simple Notification Service - Cost: $0.00', '  . Amazon Simple Storage Service - Cost: $0.05', 'Total cost: $41.71', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 339644262595', 'apm_id: APM1006706', 'application_name: Carespre-Gold', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Key Management Service - Cost: $8.15', '  . AWS Lambda - Cost: $2.61', '  . Amazon Elastic Load Balancing - Cost: $50.24', '  . Amazon Simple Storage Service - Cost: $0.18', 'Total cost: $61.18', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 988647855354', 'apm_id: APM1006706', 'application_name: Carespre-Plat', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Key Management Service - Cost: $8.18', '  . AWS Lambda - Cost: $123.36', '  . Amazon Elastic Load Balancing - Cost: $67.92', '  . Amazon Simple Storage Service - Cost: $0.53', 'Total cost: $199.99', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 988647855354', 'apm_id: APM1006706', 'application_name: Carespre-DR', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Key Management Service - Cost: $9.18', '  . AWS Lambda - Cost: $0.11', '  . Amazon Elastic Load Balancing - Cost: $33.48', '  . Amazon Simple Storage Service - Cost: $0.13', 'Total cost: $42.90', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 939078369224', 'apm_id: APM1006270', 'application_name: DBG-Slvr', 'Month: July ', 'Cost_Usage_by_Service: ', '   . Amazon Elastic Load Balancing - Cost: $100.44', 'Total cost: $100.44', ' ', ' ', ' ', ' ', ' ', ' ', '', 'Account: 820731558663', 'apm_id: APM1006270', 'application_name: DBG-Gold', 'Month: July ', 'Cost_Usage_by_Service: ', '   . AWS Key Management Service - Cost: $2.04', '  . Amazon Elastic Load Balancing - Cost: $117.18', '  . Amazon Simple Notification Service - Cost: $0.00', '  . Amazon Simple Storage Service - Cost: $0.46', 'Total cost: $119.68', ' ', ' ', ' ', ' ', ' ', ' ']

