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



