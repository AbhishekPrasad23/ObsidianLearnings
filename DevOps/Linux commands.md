
# Print lines containing "error"
awk '/error/ {print}' filename

# Print lines NOT containing "error"
awk '!/error/ {print}' filename

# Print lines where column 2 equals "value"
awk '$2 == "value" {print}' filename

# Print lines where column 1 is greater than 100
awk '$1 > 100 {print}' filename