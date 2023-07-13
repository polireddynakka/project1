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






