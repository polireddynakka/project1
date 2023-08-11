
def format_blocks(data):
    blocks = data.split("\n\n")
    lines = [block.split('\n') for block in blocks]
    max_lines = max(len(block) for block in lines)
    
    formatted_output = ""
    
    for i in range(max_lines):
        for block in lines:
            if i < len(block):
                formatted_output += block[i].ljust(40) + " | "
            else:
                formatted_output += " " * 40 + " | "
        formatted_output += "\n"
    
    return formatted_output

input_data = """[Your provided data here]"""
formatted_output = format_blocks(input_data)
print(formatted_output)
