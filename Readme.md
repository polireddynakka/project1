import json
import boto3

input_data = []

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





