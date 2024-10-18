#Rule Engine with AST
# Rule Engine with Abstract Syntax Tree (AST)

class Node:
    def __init__(self, node_type, left=None, right=None, value=None):
        """
        Initialize a Node in the Abstract Syntax Tree.
        :param node_type: "operator" or "operand"
        :param left: Left child node
        :param right: Right child node
        :param value: Condition for operands or operator for operators
        """
        self.node_type = node_type  # "operator" or "operand"
        self.left = left  # Left child node (for operators)
        self.right = right  # Right child node (for operators)
        self.value = value  # Condition (for operands)

# Helper function to evaluate conditions
def evaluate_condition(condition, user_data):
    field, operator, value = condition.split()
    value = int(value)
    if operator == '>':
        return user_data[field] > value
    elif operator == '<':
        return user_data[field] < value
    elif operator == '=':
        return user_data[field] == value
    return False

# Evaluate a rule by traversing the AST
def evaluate_rule(node, user_data):
    if node.node_type == "operand":
        return evaluate_condition(node.value, user_data)
    elif node.node_type == "operator":
        left_result = evaluate_rule(node.left, user_data)
        right_result = evaluate_rule(node.right, user_data)
        if node.value == "AND":
            return left_result and right_result
        elif node.value == "OR":
            return left_result or right_result

# Create a basic AST node manually (for simplicity)
def create_rule(rule_string):
    if rule_string == "((age > 30 AND department = 'Sales') OR (age < 25 AND department = 'Marketing')) AND (salary > 50000 OR experience > 5)":
        # Hardcoded AST for example rule 1
        left1 = Node("operand", value="age > 30")
        right1 = Node("operand", value="department = Sales")
        left2 = Node("operand", value="age < 25")
        right2 = Node("operand", value="department = Marketing")
        and_node1 = Node("operator", left1, right1, "AND")
        and_node2 = Node("operator", left2, right2, "AND")
        or_node1 = Node("operator", and_node1, and_node2, "OR")
        left3 = Node("operand", value="salary > 50000")
        right3 = Node("operand", value="experience > 5")
        or_node2 = Node("operator", left3, right3, "OR")
        final_and = Node("operator", or_node1, or_node2, "AND")
        return final_and

# Test with example user data
if __name__ == "__main__":
    user_data = {"age": 35, "department": "Sales", "salary": 60000, "experience": 3}
    # Create rule and evaluate it
    ast = create_rule("((age > 30 AND department = 'Sales') OR (age < 25 AND department = 'Marketing')) AND (salary > 50000 OR experience > 5)")
    result = evaluate_rule(ast, user_data)
    print(f"Is user eligible? {result}")  # Expected output: True
