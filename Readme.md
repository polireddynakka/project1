TimePeriod: 2023-07-01 - 2023-08-01
Service: AWS Key Management Service Cost: $1.1
Service: AWS Lambda Cost: $0.11
Service: Amazon Elastic Load Balancing Cost: $0.11
Service: Amazon Simple Storage Service Cost: $0.11
Total cost: $5
TimePeriod: 2023-07-01 - 2023-08-01
Service: Amazon Elastic Load Balancing Cost: $1
Total cost: $1
TimePeriod: 2023-07-01 - 2023-08-01
Service: AWS Key Management Service Cost: $2
Service: Amazon Elastic Load Balancing Cost: $1
Service: Amazon Simple Notification Service Cost: $0.00
Service: Amazon Simple Storage Service Cost: $0.46
Total cost: $4


 "errorMessage": "Syntax error in module 'lambda_function': invalid syntax (lambda_function.py, line 199)",
  "errorType": "Runtime.UserCodeSyntaxError",
  "requestId": "dc3d433b-0da7-451f-a24e-2a66119ba0c6",
  "stackTrace": [
    "  File \"/var/task/lambda_function.py\" Line 199\n            print(line.1just(max_length), end=' ')\n"
  ]
}


responses = [
    "TimePeriod: 2023-07-01 - 2023-08-01",
    "Service: AWS Key Management Service Cost: $1.1",
    "Service: AWS Lambda Cost: $0.11",
    "Service: Amazon Elastic Load Balancing Cost: $0.11",
    "Service: Amazon Simple Storage Service Cost: $0.11",
    "Total cost: $5",
    "TimePeriod: 2023-07-01 - 2023-08-01",
    "Service: Amazon Elastic Load Balancing Cost: $1",
    "Total cost: $1",
    "TimePeriod: 2023-07-01 - 2023-08-01",
    "Service: AWS Key Management Service Cost: $2",
    "Service: Amazon Elastic Load Balancing Cost: $1",
    "Service: Amazon Simple Notification Service Cost: $0.00",
    "Service: Amazon Simple Storage Service Cost: $0.46",
    "Total cost: $4"
]

# Separate blocks starting from TimePeriod
blocks = []
block = []
for response in responses:
    if response.startswith("TimePeriod"):
        if block:
            blocks.append(block)
        block = [response]
    else:
        block.append(response)
blocks.append(block)

# Calculate the maximum length of each line in all blocks
max_length = max(len(line) for block in blocks for line in block)

# Print the blocks side by side
for i in range(0, len(blocks[0])):
    for block in blocks:
        line = block[i] if i < len(block) else ''
        print(line.ljust(max_length), end='   ')
    print()
