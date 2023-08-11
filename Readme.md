def format_blocks(data):
    blocks = data.strip().split('\n\n')
    block_lines = [block.split('\n') for block in blocks]
    
    max_lines = max(len(block) for block in block_lines)
    max_column_width = 40
    
    formatted_output = ''
    
    for line_index in range(max_lines):
        for block in block_lines:
            if line_index < len(block):
                formatted_output += block[line_index].ljust(max_column_width) + ' | '
            else:
                formatted_output += ' ' * max_column_width + ' | '
        formatted_output += '\n'
    
    return formatted_output

input_data = """
[Your provided input data here]
"""

formatted_output = format_blocks(input_data)
print(formatted_output)
