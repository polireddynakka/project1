def format_to_human_readable(data_list):
    formatted_data = '\n'.join(data_list)
    return formatted_data

def format_to_outlook_supported(data_list):
    formatted_data = "\n".join(data_list)
    formatted_data = formatted_data.replace(". ", "\n  . ")
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



