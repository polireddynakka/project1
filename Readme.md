from prettytable import PrettyTable

# Data for your table
data = [
    {"Account": "123", "Application Name": "Slvr", "Month": "July", "Total Cost": "$0.00"},
    {"Account": "456", "Application Name": "Gold", "Month": "July", "Total Cost": "$291.92"},
    # ... (other data entries)
]

# Create the main table (same as before)
main_table = PrettyTable()
main_table.field_names = ["Account", "Application Name", "Month", "Total Cost"]

for entry in data:
    main_table.add_row([entry["Account"], entry["Application Name"], entry["Month"], entry["Total Cost"]])

# Data for your service-specific tables
service_data = [
    {"Account": "123", "Application Name": "Gold", "Service Name": "AWS Glue", "Cost": "$471.65"},
    {"Account": "123", "Application Name": "Gold", "Service Name": "AWS Glue", "Cost": "$471.65"},
    # ... (other service data entries)
]

# Create the service-specific tables with duplicate handling
service_tables = {}

for entry in service_data:
    key = (entry["Account"], entry["Application Name"])
    if key not in service_tables:
        service_tables[key] = PrettyTable()
        service_tables[key].field_names = ["Account", "Application Name", "Service Name", "Cost"]
    # Check for duplicates before adding
    new_row = [entry["Account"], entry["Application Name"], entry["Service Name"], entry["Cost"]]
    if new_row not in service_tables[key].get_rows():
        service_tables[key].add_row(new_row)

# Print the tables
print("Main Table:")
print(main_table)
print()

print("Service-Specific Tables:")
for service_table in service_tables.values():
    print(service_table)
    print()



{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "route53:ChangeResourceRecordSets",
        "route53:GetChange",
        "route53:ListResourceRecordSets",
        "route53:ListHostedZones"
      ],
      "Resource": "*"
    }
  ]
}


assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      {
        Action = "sts:AssumeRole",
        Effect = "Allow",
        Principal = {
          Service = "ec2.amazonaws.com"  # Assuming EC2 instance will use this role
        }
      }
    ]
  })
}

[('Cost_Usage_by_Service', ''), ('AWS Glue - Cost', '$1.83'), ('AWS Key Management Service - Cost', '$2.94'), ('AWS Secrets Manager - Cost', '$0.00'), ('Amazon Athena - Cost', '$0.01'), ('Amazon DocumentDB (with MongoDB compatibility) - Cost', '$3.73'), ('EC2 - Other - Cost', '$4.16'), ('Amazon Elastic Compute Cloud - Compute - Cost', '$5.66'), ('Amazon Elastic Load Balancing - Cost', '$6.22'), ('Amazon OpenSearch Service - Cost', '$7.56'), ('Amazon Simple Storage Service - Cost', '$8.71'), ('AmazonCloudWatch - Cost', '$9.56'), ('Total cost', '$10.38'), ('Cost_Usage_by_Service', ''), ('AWS Glue - Cost', '$0.09'), ('AWS Key Management Service - Cost', '$1.53'), ('AWS Lambda - Cost', '$0.00'), ('Amazon Athena - Cost', '$0.00'), ('EC2 - Other - Cost', '$2.48'), ('Amazon Elastic Compute Cloud - Compute - Cost', '$3.89'), ('Amazon OpenSearch Service - Cost', '$4.52'), ('Amazon Simple Storage Service - Cost', '$5.43'), ('AmazonCloudWatch - Cost', '$6.27'), ('Total cost', '$7.21')]


data = [
    ('Cost_Usage_by_Service', ''),
    ('AWS Glue - Cost', '$1.83'),
    ('AWS Key Management Service - Cost', '$2.94'),
    ('AWS Secrets Manager - Cost', '$0.00'),
    ('Amazon Athena - Cost', '$0.01'),
    ('Amazon DocumentDB (with MongoDB compatibility) - Cost', '$3.73'),
    ('EC2 - Other - Cost', '$4.16'),
    ('Amazon Elastic Compute Cloud - Compute - Cost', '$5.66'),
    ('Amazon Elastic Load Balancing - Cost', '$6.22'),
    ('Amazon OpenSearch Service - Cost', '$7.56'),
    ('Amazon Simple Storage Service - Cost', '$8.71'),
    ('AmazonCloudWatch - Cost', '$9.56'),
    ('Total cost', '$10.38'),
    ('Cost_Usage_by_Service', ''),
    ('AWS Glue - Cost', '$0.09'),
    ('AWS Key Management Service - Cost', '$1.53'),
    ('AWS Lambda - Cost', '$0.00'),
    ('Amazon Athena - Cost', '$0.00'),
    ('EC2 - Other - Cost', '$2.48'),
    ('Amazon Elastic Compute Cloud - Compute - Cost', '$3.89'),
    ('Amazon OpenSearch Service - Cost', '$4.52'),
    ('Amazon Simple Storage Service - Cost', '$5.43'),
    ('AmazonCloudWatch - Cost', '$6.27'),
    ('Total cost', '$7.21')
]

result = []
current_group = []

for item in data:
    label, value = item
    if label == 'Cost_Usage_by_Service':
        if current_group:  # Check if the current group is not empty
            result.append(current_group)
        current_group = [item]
    elif label == 'Total cost':
        current_group.append(item)
        result.append(current_group)
        current_group = []
    else:
        current_group.append(item)

# Print the separated lists
for group in result:
    print(group)

