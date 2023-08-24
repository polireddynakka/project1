from tabulate import tabulate
from rich import print
from microsoftteams import Teams

# Your input data
input_data = """
apm_id: APM1003015
Account: 993514063544
application_name: Provider-Finder-Slvr
Month: July
Cost_Usage_by_Service:
Total cost: $0.00
# ... (other data entries)
"""

# Process input data
entries = input_data.strip().split("\n\n")
formatted_entries = []

for entry in entries:
    lines = entry.strip().split("\n")
    entry_data = {}
    for line in lines:
        key, value = map(str.strip, line.split(":", 1))
        entry_data[key] = value
    formatted_entries.append(entry_data)

# Format data into tabular format
table_data = []
for entry in formatted_entries:
    entry_table = [
        ["[bold]" + key, value] for key, value in entry.items()
    ]
    table_data.append(entry_table)
    table_data.append([])  # Empty row to separate entries

# Convert table data to tabular format
table_str = ""
for entry_table in table_data:
    table_str += tabulate(entry_table, tablefmt="plain") + "\n"

# Print formatted output
print(table_str)

# Send the formatted output to Microsoft Teams
teams = Teams("<YOUR_WEBHOOK_URL>")  # Replace with your Teams webhook URL
message = {"text": "```" + table_str + "```"}
teams.send_message(message)
