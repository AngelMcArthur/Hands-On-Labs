# **Multi‑Threaded Python Port Scanner**

- **Summary:** Build a high‑performance TCP port scanner in Python that resolves hostnames, uses socket programming, and launches concurrent threads to scan all 65,535 ports rapidly.

- **Key Skills/Tools:** Python, `socket` programming, multithreading, error handling

This is a capstone project from the Programming 100 Fundamentals Course by [TCM Security](https://academy.tcm-sec.com/). 

---


- *(Note - **==STEP 1 - CREATE A NEW FILE**==. In Visual Studio Code click the "File" tab in the upper left corner. Then from the drop down menu click "New File...". A few options should pop up in the middle search bar, but we will select "Python File". Now that you have a new file, save it with "ctrl+s". Name it what you like, but we will be calling it "scanner" from here on out.)*
	- ![[Pasted image 20250111014115.png]]
	- ![[Pasted image 20250110193357.png]]
- *(Note - **==STEP 2 - IMPORT MODULES**==. To get started with the scanner project, we will need to add some functionality via modules. To do that we import the following: `import sys`, `import socket`, `from datetime import datetime`, and `import threading`.)*
	- ![[Pasted image 20250111014217.png]]
		- **`import sys`:** The `sys` module provides access to system-specific parameters and functions, essential for interacting with the operating system. In this program, `sys` enables the handling of command-line arguments, allowing users to input a target hostname or IP address when running the script. This facilitates dynamic user input without hardcoding values. Additionally, the `sys.exit()` method ensures that the program can terminate cleanly if errors or invalid input occur, preventing unnecessary execution. Overall, `sys` bridges the gap between the script and the system environment, enabling flexible and robust functionality.
			- *(**TLDR** - This module ==provides access to system-specific parameters== and functions, such as command-line arguments or program termination.)*
		- **`import socket`:** The `socket` module is a cornerstone of network programming in Python, providing the tools needed to create and manage sockets. A socket is a virtual connection endpoint used for sending and receiving data over a network. By importing this module, the script gains the ability to establish and test connections to specific ports on a target host. It supports a wide range of networking protocols, and in this program, TCP is used to check if ports are open or closed. This functionality forms the backbone of the port scanning process.
			- *(**TLDR** - This module ==allows the program to interact with network interfaces==, enabling socket creation and communication.)*
		- **`from datetime import datetime`:** The `datetime` module provides a range of functions to handle dates and times in Python. By importing the `datetime` class, the script can retrieve the current date and time, which is used here to timestamp the start of the port scan. This information is not just a decorative feature but provides context about when the scan was initiated, which can be valuable for tracking purposes in larger security assessments or debugging. It also adds professionalism to the program output, making it easier to read and interpret.
			- *(**TLDR** - This imports the `datetime` class to capture and ==display the current date and time== for the scan.)*
		- **`import threading`:** The `threading` module allows the program to perform multiple tasks concurrently by creating threads. A thread is a lightweight subprocess that can run independently within a program. In the context of the port scanner, threads enable simultaneous scanning of multiple ports, drastically reducing the time required to complete the scan. Without threading, the script would have to scan ports sequentially, which could take hours for a full range scan of all 65,535 ports. By leveraging this module, the program efficiently handles large workloads while maintaining responsiveness.
			- *(**TLDR** - This module ==enables the creation of threads==, allowing concurrent execution of multiple parts of the program, which is essential for efficient port scanning.)*

- *(Note - **==STEP 3 - CREATE A PORT SCANNING FUNCTION**==. This section defines the `scan_port` function, which is responsible for scanning individual ports on a given target IP address. It creates a socket, attempts a connection to the specified port, and determines whether the port is open based on the connection result. The function handles errors like socket failures and other unexpected issues, ensuring that the program continues running smoothly without crashing.)*
	- ![[Pasted image 20250111014505.png]]
		- `def scan_port(target, port):`:This line defines a function called `scan_port`, which is responsible for attempting to connect to a specific port on the target host. Functions allow reusable blocks of code, and this particular function isolates the logic for checking a port’s status. By providing the target IP address and port number as arguments, it ensures that the scanning logic can be executed independently for any given port.
			- *(**TLDR** - Defines the `scan_port` function to check the status of a specific port on the target host.)*
	- ![[Pasted image 20250111014544.png]]
		- `try:`: The `try` keyword begins a block of code that will be monitored for exceptions—unexpected errors that might occur during execution. It allows the program to attempt operations that could fail (like creating a socket or connecting to a port) without immediately stopping the program if an error occurs. If an exception is raised within the `try` block, the program will jump to the corresponding `except` block to handle the error gracefully, ensuring the script remains robust and user-friendly.
			- *(**TLDR** - Starts a block to monitor for and handle errors during potentially risky operations.)*
		- `s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)`: A socket is a virtual connection point that enables two programs to communicate over a network. The line `s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)` creates a new socket object in Python using the `socket` module. The `AF_INET` parameter specifies the use of IPv4 addresses, while `SOCK_STREAM` designates a TCP socket, ensuring reliable, ordered communication. This socket will be used to attempt connections to specific ports on a target machine. If the connection succeeds, the port is open; if it fails, the port is likely closed or unreachable. This forms the foundation for the port scanner’s functionality.
			- *(**TLDR** - Creates a TCP socket for network communication using IPv4 addresses.)*
		- `s.settimeout(1)`: The `settimeout` method configures the socket to wait a maximum of 1 second for a response from the target. This ensures the program doesn’t hang indefinitely on unresponsive ports, which is critical for performance in a port scanner that might check thousands of ports. A timeout prevents unnecessary delays, allowing the script to move on to the next port efficiently if no connection can be established within the specified time. It balances responsiveness and thoroughness in the scanning process.
			- *(**TLDR** - Sets a 1-second timeout for the socket to prevent hanging on unresponsive ports.)*
		- `result = s.connect_ex((target, port))`: The `connect_ex` method attempts to establish a connection to the specified target IP and port. Unlike `connect`, it doesn’t raise an exception if the connection fails; instead, it returns an error code. A return value of `0` indicates a successful connection, meaning the port is open. Non-zero values signify failure, which could mean the port is closed, filtered by a firewall, or otherwise unreachable. This approach allows the program to handle port connectivity checks gracefully and efficiently.
			- *(**TLDR** - Attempts to connect to the target port, returning `0` if the port is open and non-zero otherwise.)*
		- `if result == 0:`: This conditional statement evaluates the result of the `connect_ex` method. If the result is `0`, the program determines that the port is open and ready to accept connections. This check ensures that only open ports are reported to the user, filtering out closed or unreachable ports. It’s a critical decision-making point in the function, driving the meaningful output of the scanner.
			- *(**TLDR** - Checks if the connection was successful to determine if the port is open.)*
		- `print(f"Port {port} is open")`: This line prints a message indicating that the specified port is open. By including the port number in the formatted string, the program provides clear, actionable information to the user. This output is the primary goal of the port scanner, allowing users to identify open ports for further investigation or validation of their network configurations.
			- *(**TLDR** - Outputs a message indicating the port is open and includes its number for clarity.)*
		- `s.close()`: Closing the socket is crucial for freeing up system resources. A socket remains a system-level resource until explicitly closed, and leaving it open can lead to resource exhaustion in the operating system, especially in high-frequency operations like port scanning. By closing the socket after checking a port, the program ensures efficient resource management and avoids potential errors related to too many open connections.
			- *(**TLDR** - Closes the socket to free up system resources after use.)*
	- ![[Pasted image 20250111014723.png]]
		- `except socket.error as e`: This line begins an exception-handling block to catch socket-related errors. Socket errors typically occur when there is an issue with network communication, such as connection failures or invalid parameters. By capturing these errors, the program can provide informative messages to the user rather than crashing unexpectedly.
			- *(**TLDR** - Catches and handles errors related to socket operations.)*
		- `print(f"Socket error on port {port}: {e}")`: This line prints a descriptive error message if a socket error occurs, including the specific port and error details. This feedback helps users understand which port caused the issue and what kind of error was encountered. Providing such information makes troubleshooting and debugging easier during network scans.
			- *(**TLDR** - Displays an error message for socket-related issues on a specific port.)*
	- ![[Pasted image 20250111020827.png]]
		- `except Exception as e`: This line catches any other exceptions not specifically related to socket errors. This is a generic safety net for unexpected issues, ensuring the program remains stable even in unforeseen situations. Catching generic exceptions also prevents the program from crashing and offers a chance to log or display useful error messages.
			- *(**TLDR** - Handles unexpected errors to maintain program stability.)*
		- `print(f"Unexpected error on port {port}: {e}")`: This outputs a message detailing any unexpected error that occurs during the port scan. Including the port number and error description provides additional context for debugging and resolving issues. This message acts as a fallback for exceptions not related to socket errors.
			- *(**TLDR** - Logs unexpected errors with details for debugging.)*
- **Here is the entire port scanning function:**
	- ![[Pasted image 20250111021228.png]]

- *(Note - ==**STEP 4 - MAIN FUNCTION: ARGUMENT VALIDATION AND TARGET DEFINITION**==. In this section, the `main` function validates the command-line arguments passed to the script. It checks if the user has provided a valid target IP or hostname, and if not, displays an error message and exits. The function also attempts to resolve the target hostname into an IP address and handles any failures that may occur during this process, ensuring that a valid target is available for scanning before proceeding.)*
	- ![[Pasted image 20250111022953.png]]
		- `def main():`: This line defines the `main` function, which serves as the central entry point of the program. Functions like `main` are used to organize code logically and enhance readability, allowing for modular programming by encapsulating the main logic of the program within a callable block. Defining a `main` function is a best practice in Python, especially for larger scripts, as it makes the program more maintainable and facilitates reuse of other parts of the code.
			- _(**TLDR** - Defines the main function, organizing the core logic of the program.)_
	- ![[Pasted image 20250111023038.png]]
		- `if len(sys.argv) == 2:`: Checks if exactly two command-line arguments are provided when the script is run. This ensures the user has supplied a target address as an argument, which is critical for the program to function. Without this check, the program might proceed without sufficient information, leading to errors or unexpected behavior.
		    - _(**TLDR** - Verifies that the script is run with exactly one target argument.)_
		- `target = sys.argv[1]`: Assigns the second command-line argument (the target address) to the `target` variable. This variable holds the input provided by the user and is later used to resolve the hostname and scan its ports.
		    - _(**TLDR** - Stores the user-provided target address for further processing.)_
	- ![[Pasted image 20250111023201.png]]
	- `else:`: Indicates the alternate path if the `if` condition fails. If the user does not supply exactly two arguments, the program provides instructions and exits gracefully.
	    - _(**TLDR** - Handles cases where the required argument is missing.)_
	- `print("Invalid number of arguments.")`: Prints an error message to inform the user about the incorrect usage of the program. This message is crucial for usability, guiding users toward correct input.
	    - _(**TLDR** - Displays an error message for incorrect argument usage.)_
	- `print("Usage: python.exe scanner.py <target>")`: Provides an example of how to run the program correctly. This clarifies the program's expected input format, improving the user experience.
	    - _(**TLDR** - Shows a usage example to guide the user.)_
	- `sys.exit(1)`: Terminates the program execution with an error code of `1`, signaling that something went wrong. Exiting here prevents the program from running with invalid inputs, which could cause errors.
	    - _(**TLDR** - Exits the program due to missing or invalid arguments.)_
- **Here is the entire Main function for argument validation and target definition:**
	- ![[Pasted image 20250111023547.png]]

- *(Note - ==**STEP 5 - HOSTNAME RESOLUTION**==. This section resolves the target hostname into an IP address. If the hostname is invalid or cannot be resolved, the program notifies the user and terminates. Hostname resolution ensures the program has a valid network address to scan.)*
	- ![[Pasted image 20250111024638.png]]
		- `try:`: Begins a block of code that attempts to resolve the target hostname into an IP address. If any errors occur during this process, the program gracefully handles them, ensuring it does not crash unexpectedly. This structure provides fault tolerance for invalid or inaccessible hostnames.
		    - _(**TLDR** - Starts a block to handle potential errors during hostname resolution.)_
		- `target_ip = socket.gethostbyname(target)`: Uses the `socket.gethostbyname()` method to convert the user-provided hostname into its corresponding IP address. This is essential because network operations typically require IP addresses rather than hostnames. By resolving the hostname, the program ensures it can locate and communicate with the target on the network.
		    - _(**TLDR** - Resolves the target hostname to an IP address for scanning.)_
	- ![[Pasted image 20250111024727.png]]
		- `except socket.gaierror:`: Catches a `gaierror`, which occurs when the hostname cannot be resolved. This ensures the program can handle invalid or unreachable hostnames without crashing.
		    - _(**TLDR** - Handles errors caused by invalid or unresolvable hostnames.)_
		- `print(f"Error: Unable to resolve hostname {target}")`: Informs the user that the specified hostname is invalid or cannot be resolved. This feedback helps users troubleshoot their input and provides clarity about the issue.
		    - _(**TLDR** - Notifies the user of an invalid or unresolvable hostname.)_
		- `sys.exit(1)`: Terminates the program with an error code of `1` if the hostname resolution fails. This prevents further execution since a valid target is required for scanning.
		    - _(**TLDR** - Exits the program when hostname resolution fails.)_
- **Here is the entire Hostname Resolution section.**
	- ![[Pasted image 20250111025529.png]]

- *(Note - ==**STEP 6 - DISPLAYING SCAN BANNER**==. This section prepares and displays a banner to inform the user about the scanning process. It includes details such as the target IP address and the start time of the scan.)*
	- ![[Pasted image 20250111030040.png]]
		- `print("-" * 50)`: Prints a horizontal divider line composed of 50 dashes. This serves as a visual separator, making the output more organized and easier to read.
		    - _(**TLDR** - Prints a separator line for better output formatting.)_
		- `print(f"Scanning target {target_ip}")`: Displays a message indicating the target IP address being scanned. This provides confirmation to the user about which system is the focus of the operation.
		    - _(**TLDR** - Notifies the user about the target being scanned.)_
		- `print(f"Time started: {datetime.now()}")`: Prints the current date and time when the scan begins. This helps the user track the duration of the scan and provides a timestamp for reference.
		    - _(**TLDR** - Outputs the scan start time for user reference.)_
		- `print("-" * 50)`: Prints another horizontal divider line to frame the banner. This improves the readability of the console output.
		    - _(**TLDR** - Prints another separator line for clarity.)_
		- `try:`: Initiates a block of code for the multithreading logic while preparing to handle exceptions that may occur during the scan. This ensures the program can gracefully recover from interruptions or errors.
			- _(**TLDR** - Begins a block to handle potential issues during the scan.)_

- *(Note - ==**STEP 7 - MULTITHREADED PORT SCANNING**==. This section implements multithreading to scan multiple ports simultaneously, significantly speeding up the scanning process. It creates and starts threads for each port within the specified range.)*
	- ![[Pasted image 20250111030631.png]]
		- `threads = []`: Initializes an empty list to store thread objects. Each thread corresponds to the scanning of a specific port.
		    - _(**TLDR** - Creates a list to keep track of all scanning threads.)_
	- ![[Pasted image 20250111030703.png]]
		- `for port in range(1, 65536):`: Starts a loop that iterates through all possible port numbers, from 1 to 65535. Each iteration represents an individual port to be scanned.
		    - _(**TLDR** - Loops through all possible port numbers for scanning.)_
		- `thread = threading.Thread(target=scan_port, args=(target_ip, port))`: Creates a new thread to run the `scan_port` function, passing the target IP and the current port number as arguments. This allows concurrent execution of the port-scanning logic.
		    - _(**TLDR** - Creates a thread for scanning a specific port.)_
		- `threads.append(thread)`: Adds the newly created thread to the `threads` list. This ensures the program can manage and track all active threads.
		    - _(**TLDR** - Stores the thread in the list for later management.)_
		- `thread.start()`: Starts the execution of the thread, initiating the scanning of the specified port. This enables concurrent port scanning, improving efficiency.
		    - _(**TLDR** - Begins the thread’s execution to scan a port.)_
- **Here is the full Multithreaded Port Scanning section.**
	- ![[Pasted image 20250111030852.png]]

- *(Note - ==**STEP 8 - WAITING FOR THREADS TO COMPLETE**==. This section ensures all threads finish their execution before the program continues, allowing the scan to complete properly before any summary or cleanup actions.)*
	- ![[Pasted image 20250111032025.png]]
		- `for thread in threads:`: Loops through each thread object stored in the `threads` list. This prepares to wait for each thread to finish its assigned task.
		    - _(**TLDR** - Iterates through all threads to monitor their progress.)_
		- `thread.join()`: Waits for the current thread to complete its execution before proceeding to the next one. This ensures all port scans are finished before the program exits or moves on to other tasks.
		    - _(**TLDR** - Waits for each thread to finish its scan before proceeding.)_

- *(Note - ==**STEP 9 - HANDLING INTERRUPTIONS AND ERRORS DURING SCANNING**==. This section gracefully handles interruptions like keyboard interrupts or socket-related errors that may occur during the scan, ensuring a clean exit.)*
	- ![[Pasted image 20250111032357.png]]
		- `except KeyboardInterrupt:`: Captures a manual keyboard interruption (e.g., Ctrl+C). This allows the program to exit gracefully without displaying a traceback error.
		    - _(**TLDR** - Handles manual interruptions like Ctrl+C gracefully.)_
		- `print("\nExiting program.")`: Prints a message indicating that the program is exiting due to a manual interruption. This provides feedback to the user.
		    - _(**TLDR** - Notifies the user about the program’s exit.)_
		- `sys.exit(0)`: Terminates the program with an exit code of 0, indicating a successful and controlled exit.
		    - _(**TLDR** - Stops the program execution cleanly.)_
		- `except socket.error as e:`: Catches any socket-related errors that may occur during the scan. This ensures the program can handle network-related issues without crashing.
		    - _(**TLDR** - Manages errors related to socket operations.)_
		- `print(f"Socket error: {e}")`: Prints a descriptive error message, including details about the socket error. This helps users understand what went wrong.
		    - _(**TLDR** - Displays a detailed message for socket-related errors.)_
		- `sys.exit(1)`: Terminates the program with an exit code of 1, signaling an error occurred during execution.
		    - _(**TLDR** - Exits the program due to a socket error.)_

- *(Note - ==**STEP 10 - COMPLETING THE SCAN**==. This final section prints a message indicating the successful completion of the scan process.)*
	- ![[Pasted image 20250111033143.png]]
		- `print("\nScan completed!")`: Prints a message to the user confirming the completion of the port scan. The newline at the beginning ensures the message appears on a new line, separated from prior output.
			- _(**TLDR** - Confirms the port scan has successfully completed.)_

- *(Note - ==**STEP 11 - ENTRY POINT FOR EXECUTION**==. This section defines the entry point of the program, ensuring the main function is executed when the script is run directly.)*
	- ![[Pasted image 20250111033613.png]]
		- `if __name__ == "__main__":`: Checks if the script is being executed as the main program (not imported as a module). This ensures the `main()` function runs only when the script is directly invoked.
		    - _(**TLDR** - Ensures the main function is executed when the script runs directly.)_
		- `main()`: Calls the `main()` function to start the port scanner logic. This initiates the entire scanning process.
		    - _(**TLDR** - Starts the port scanner execution.)_

- *(Note - ==STEP 12 - RUN IT!==. Test it out.)*
	- Open your cmd in Windows or terminal in Linux. We'll be using Windows for the demonstration. Run `ipconfig` in the terminal. You should see something like this:
		- ![[Pasted image 20250111182118.png]]
	- We are looking for the IPv4 Address since we will be scanning our own host:
		- ![[Pasted image 20250111182327.png]]
	- Head back to Visual Studio Code and run the scanner using your ip address.
		- ![[Pasted image 20250111183103.png]]
	- Here are the results and the conclusion of the walk-through!:
		- ![[Pasted image 20250111183425.png]]
		- ![[Pasted image 20250111183502.png]]

---