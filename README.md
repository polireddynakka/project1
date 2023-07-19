# project1
learning for git 
from hcl2 import loads, dumps

def remove_variables(file_path, variables_to_remove):
    with open(file_path, 'r') as f:
        hcl_content = f.read()

    tf_data = loads(hcl_content)

    # Traverse the parsed HCL data and remove specified variables
    remove_variables_recursively(tf_data, variables_to_remove)

    updated_hcl_content = dumps(tf_data)

    with open(file_path, 'w') as f:
        f.write(updated_hcl_content)

def remove_variables_recursively(tf_data, variables_to_remove):
    if isinstance(tf_data, dict):
        for key, value in list(tf_data.items()):
            if key in variables_to_remove:
                del tf_data[key]
            else:
                remove_variables_recursively(value, variables_to_remove)
    elif isinstance(tf_data, list):
        for item in tf_data:
            remove_variables_recursively(item, variables_to_remove)

# Usage example
file_path = 'path/to/terraform_file.tf'
variables_to_remove = ['variable1', 'variable2']

remove_variables(file_path, variables_to_remove)




import re

def remove_matching_variables(filename, expression):
    # Read the Terraform file
    with open(filename, 'r') as file:
        data = file.read()

    # Define the regular expression pattern
    pattern = r'variable "(.*?)" {.*?\n.*?default = (.*?)\n'

    # Find all variable matches
    matches = re.findall(pattern, data, re.DOTALL)

    # Iterate over the matches and remove variables that match the expression
    for match in matches:
        variable_name = match[0]
        variable_value = match[1]

        # Check if the variable matches the expression
        if re.search(expression, variable_name):
            # Remove the variable declaration and its default value
            data = data.replace('variable "{}" {{\n  default = {}\n}}'.format(variable_name, variable_value), '')

    # Write the modified data back to the file
    with open(filename, 'w') as file:
        file.write(data)

# Example usage
filename = 'terraform.tf'
expression = r'^my_'
remove_matching_variables(filename, expression)




from hcl2 import decode, encode, writer

def remove_variables(file_path, variables_to_remove):
    with open(file_path, 'r') as f:
        hcl_content = f.read()

    tf_data = decode(hcl_content)

    # Traverse the parsed HCL data and remove specified variables
    remove_variables_recursively(tf_data, variables_to_remove)

    updated_hcl_content = encode(tf_data, writer=writer())

    with open(file_path, 'w') as f:
        f.write(updated_hcl_content)

def remove_variables_recursively(tf_data, variables_to_remove):
    if isinstance(tf_data, dict):
        for key, value in list(tf_data.items()):
            if key in variables_to_remove:
                del tf_data[key]
            else:
                remove_variables_recursively(value, variables_to_remove)
    elif isinstance(tf_data, list):
        for item in tf_data:
            remove_variables_recursively(item, variables_to_remove)

# Usage example
file_path = 'path/to/terraform_file.tf'
variables_to_remove = ['variable1', 'variable2']

remove_variables(file_path, variables_to_remove)





#########

from tfparser import terraform

def remove_variables(file_path, variables_to_remove):
    with open(file_path, 'r') as f:
        tf_content = f.read()

    tf_data = terraform.parse(tf_content)

    # Traverse the parsed Terraform data and remove specified variables
    remove_variables_recursively(tf_data, variables_to_remove)

    updated_tf_content = tf_data.dumps()

    with open(file_path, 'w') as f:
        f.write(updated_tf_content)

def remove_variables_recursively(tf_data, variables_to_remove):
    if isinstance(tf_data, terraform.Block):
        if tf_data.type == "variable" and tf_data.name in variables_to_remove:
            tf_data.parent.body.remove(tf_data)
        else:
            for child_block in tf_data.body:
                remove_variables_recursively(child_block, variables_to_remove)
    elif isinstance(tf_data, list):
        for item in tf_data:
            remove_variables_recursively(item, variables_to_remove)

# Usage example
file_path = 'path/to/terraform_file.tf'
variables_to_remove = ['variable1', 'variable2']

remove_variables(file_path, variables_to_remove)




import re

def remove_variables(file_path, variables_to_remove):
    with open(file_path, 'r') as f:
        tf_content = f.read()

    for variable in variables_to_remove:
        regex = r"variable\s+\"{}\"".format(re.escape(variable))
        tf_content = re.sub(regex, "", tf_content)

    with open(file_path, 'w') as f:
        f.write(tf_content)

# Usage example
file_path = 'path/to/terraform_file.tf'
variables_to_remove = ['variable1', 'variable2']

remove_variables(file_path, variables_to_remove)


import re

def remove_variables(file_path, variables_to_remove):
    with open(file_path, 'r') as f:
        tf_content = f.read()

    for variable in variables_to_remove:
        regex = r"variable\s+\"{}\"\s*{{[^}}]*}}".format(re.escape(variable))
        tf_content = re.sub(regex, "", tf_content, flags=re.DOTALL)

    with open(file_path, 'w') as f:
        f.write(tf_content)

# Usage example
file_path = 'path/to/terraform_file.tf'
variables_to_remove = ['variable1', 'variable2']

remove_variables(file_path, variables_to_remove)



def add_variable(file_path, variable_name):
    with open(file_path, 'r') as f:
        tf_content = f.readlines()

    # Check if the variable already exists
    existing_variable = False
    for line in tf_content:
        if line.strip().startswith("variable") and variable_name in line:
            existing_variable = True
            break

    # If the variable does not exist, add a new block
    if not existing_variable:
        tf_content.append("\nvariable \"{}\" {{\n".format(variable_name))
        tf_content.append("  type = any\n")
        tf_content.append("  default = []\n")
        tf_content.append("}\n")

        with open(file_path, 'w') as f:
            f.writelines(tf_content)
    else:
        print("The variable already exists in the file. Skipping addition of the variable.")

# Usage example
file_path = 'path/to/terraform_file.tf'
variable_name = 'example_variable'

add_variable(file_path, variable_name)



def remove_consecutive_empty_lines(filename, n):
    with open(filename, 'r') as file:
        lines = file.readlines()

    output_lines = []
    consecutive_empty_lines = 0

    for line in lines:
        if line.strip() == '':
            consecutive_empty_lines += 1
        else:
            if consecutive_empty_lines <= n:
                output_lines.extend(['\n'] * consecutive_empty_lines)
            consecutive_empty_lines = 0
            output_lines.append(line)

    if consecutive_empty_lines <= n:
        output_lines.extend(['\n'] * consecutive_empty_lines)

    with open(filename, 'w') as file:
        file.writelines(output_lines)

# Usage example
filename = 'example.txt'
n = 3
remove_consecutive_empty_lines(filename, n)



def process_file(filename, words_to_delete, words_to_replace, replacement_lines):
    with open(filename, 'r') as file:
        lines = file.readlines()

    output_lines = []

    for line in lines:
        should_delete = False
        for word in words_to_delete:
            if word in line:
                should_delete = True
                break

        if should_delete:
            continue

        should_replace = False
        for word in words_to_replace:
            if word in line:
                should_replace = True
                break

        if should_replace:
            output_lines.extend(replacement_lines)
        else:
            output_lines.append(line)

    with open(filename, 'w') as file:
        file.writelines(output_lines)

# Usage example
filename = 'example.txt'
words_to_delete = ['word1', 'word2', 'word3']
words_to_replace = ['word4', 'word5']
replacement_lines = ['Replacement line 1\n', 'Replacement line 2\n']

process_file(filename, words_to_delete, words_to_replace, replacement_lines)




import hcl
import json

def modify_module_variables(module_file, variables_to_add, variables_to_delete):
    # Load the module file as HCL
    with open(module_file, 'r') as f:
        hcl_data = hcl.load(f)

    # Find existing variables in the module
    existing_variables = hcl_data['module']['my_s3_bucket']['variable']

    # Add new variables to the module
    for variable, value in variables_to_add.items():
        existing_variables[variable] = {'default': value}

    # Delete variables from the module
    for variable in variables_to_delete:
        existing_variables.pop(variable, None)

    # Serialize the modified HCL back to the module file
    with open(module_file, 'w') as f:
        json.dump(hcl_data, f, indent=2)

# Example usage
module_file = 'main.tf'
variables_to_add = {'new_variable': 'new_value'}
variables_to_delete = ['environment']

modify_module_variables(module_file, variables_to_add, variables_to_delete)


def add_variable_block(file_path, condition_variable, new_variable_name):
    with open(file_path, 'r') as f:
        tf_content = f.readlines()

    # Check if the condition_variable is present in the file
    if condition_variable in tf_content:
        # Check if the new_variable_name already exists
        if "variable \"{0}\"".format(new_variable_name) not in tf_content:
            new_variable_block = "\nvariable \"{0}\" {{\n  type = any\n  default = [{1}]\n}}\n".format(new_variable_name, "block = [{")
            tf_content.append(new_variable_block)

            with open(file_path, 'w') as f:
                f.writelines(tf_content)
            print("Variable block added successfully.")
        else:
            print("Variable block already exists.")
    else:
        print("Condition variable not found.")

# Usage example
file_path = 'path/to/terraform_file.tf'
condition_variable = "existing_variable"
new_variable_name = "new_variable"

add_variable_block(file_path, condition_variable, new_variable_name)



def add_tree_block(file_path, condition_key, condition_value, new_block_content):
    with open(file_path, 'r') as f:
        tf_content = f.readlines()

    # Check if the condition_key and condition_value are present in the file
    if condition_key in tf_content and condition_value in tf_content:
        new_line_index = 0

        # Find the index where the new block should be inserted
        for i, line in enumerate(tf_content):
            if line.strip() == condition_key and tf_content[i+1].strip() == condition_value:
                new_line_index = i + 2  # Insert new block after condition key-value

        # Insert the new block at the calculated index
        tf_content.insert(new_line_index, new_block_content)

        with open(file_path, 'w') as f:
            f.writelines(tf_content)
        print("New block added successfully.")
    else:
        print("Condition key-value not found.")

# Usage example
file_path = 'path/to/terraform_file.tf'
condition_key = "create"
condition_value = "true"
new_block_content = '''
tree = [{
    name = "poli"
    dob = "feb"
    state = "VA"
    poli = {
            haa = 1
            hello = 2
     }
}]
'''

add_tree_block(file_path, condition_key, condition_value, new_block_content)




Confluent Documentation:

[Confluent Kafka Documentation](https://docs.confluent.io/home/overview.html)
[Confluent Kafka Clients for Various Programming Languages](https://docs.confluent.io/platform/current/clients/index.html)


bootstrap: SSL handshake failed: Disconnected: connecting to a PLAINTEXT broker listener? (after 27ms in state SSL_HANDSHAKE, 14 identical error(s) suppressed)




