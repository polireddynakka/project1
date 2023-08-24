import re
import json

data = """
# ... (your input data here)
"""

entries = re.split(r"\n\s*\n", data.strip())
result = {}

for entry in entries:
    entry_lines = entry.strip().split("\n")
    entry_dict = {}

    for line in entry_lines:
        if line.startswith("apm_id"):
            apm_id = line.split(":")[1].strip()
        elif line.startswith("Account"):
            entry_dict["Account"] = line.split(":")[1].strip()
        elif line.startswith("application_name"):
            entry_dict["application_name"] = line.split(":")[1].strip()
        elif line.startswith("Month"):
            entry_dict["Month"] = line.split(":")[1].strip()
        elif line.startswith("Total cost"):
            entry_dict["Total cost"] = line.split(":")[1].strip()
        elif re.match(r"\s*\.\s", line):
            service, cost = line.split(" - Cost: ")
            entry_dict.setdefault("Cost_Usage_by_Service", []).append({service.strip(): cost.strip()})

    if apm_id not in result:
        result[apm_id] = []

    result[apm_id].append(entry_dict)

json_result = json.dumps(result, indent=2)
print(json_result)
