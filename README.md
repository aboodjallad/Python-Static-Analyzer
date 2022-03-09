# Python Static Analyzer
### Part 1: Reading (.py) the file as text
- Using "with open", we select the file's path and pass on the "r" (which is read) value, then name the opened file as file. Then we exported each line from the now "text file", and added each line to a list of lines. Then, using the indentation, we performed the above mentioned operation while the file was open, and closed when the operations within the indentation were done
```ruby
with open("File/Test.py", "r") as file:
    lines = [line.rstrip() for line in file]
```
### Part 2: Coding the Checkers
#### 1- Checking for invalid arguments in function calls
- The "parameter_check" function iterates through each line in the list of lines and checks every line for a function definition keyword (which is "def") with a regular expression using the re library and appends that function along with its parameters to a list called "functions"
- Then it takes all the defined functions from "functions list" and appends their only the function names to a new list called "functions_names"
- Then the function reiterates through the lines of code to search for the functions' names along with their first paranthesis "(" to check for function calls and appends them to the "function_calls" list if found
- Finally the function iterates through the called functions lists and all the defined functions list, where it checks if the called's function's name is in the functions' definitions. And if it's true, it takes the function's required parameters from the definition, and function call's passed arguments. Then it checks for the length of the passed arguments in comparison to the required parameters' length. After that, it checks every argument's value if it's found in any of the function's parameters (since Python allows to pass the needed arguments without the right order)
```ruby
def parameter_check():
    function_calls = []
    functions_names = []
    functions = []
    used_functions = []
    for line in FileReader.lines:
        if "def" in line:
            try:
                functions.append((re.findall(r'def (.+?):', line))[0])
            except:
                break
    for function in functions:
        try:
            functions_names.append(re.findall(r'(.+?)\(', function)[0])
        except:
            break

    for line in FileReader.lines:
        for function in functions_names:
            if f'{function}(' in line:
                if "def" not in line:
                    try:
                        function_calls.append(re.findall(fr'({function}.+?\))', line)[0])
                    except:
                        break

    for called_function in function_calls:
        function_name = re.findall(r'(.+?)\(', called_function)[0]
        for function in functions:
            if function_name in function:
                try:
                    parameters = re.findall(r'\((.+?)\)', function)[0].replace(" ", "").split(',')
                except:
                    parameters = [""]
                try:
                    arguments = re.findall(r'\((.+?)\)', called_function)[0].replace(" ", "").split(',')
                except:
                    arguments = [""]
                if len(arguments) != len(parameters):
                    warning_calls.append(f"Invalid arguments for {called_function} at line {(FileReader.lines.index(called_function)) + 1}")
                for argument in arguments:
                    if not argument.isdigit() and not is_float(argument) and "string" in parameters:
                        continue
                    if argument.isdigit and "int" in parameters:
                        continue
                    if is_float(argument) and "float" in parameters:
                        continue
                    else:
                        warning_calls.append(f"Invalid arguments for {called_function} at line {(FileReader.lines.index(called_function)) + 1}")
    return warning_calls
```
###
#### 2- Checking for unreachable code
- The function "unreachable_code" iterates through each line in the list of line (but first, it filters the "lines" list from empty lines, since we need to capture every executable line after the stop statements such as "return", "break", "continue") 
- Then it checks if there is a line of code after the stop statement (couldn't figure out how to take all the lines of code after the stop statement because of indentations) and adds it to the "unreachable_lines" list
```ruby
def unreachable_code():
    lines = list(filter(None, FileReader.lines))
    unreachable_lines = []
    for line in lines:
        if "return" in line or "break" in line or "continue" in line:
            spaces_count = len(line) - len(line.strip())
            if lines[lines.index(line) + 1].startswith(" " * spaces_count):
                unreachable_lines.append(lines[lines.index(line) + 1].strip() + " is unreachable")
    print(unreachable_lines)
```
