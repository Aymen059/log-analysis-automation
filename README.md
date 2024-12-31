Log Analysis and Automated IP Blocking

Project Overview

This project demonstrates a practical cybersecurity task involving SSH log analysis and automated IP blocking on a Linux server. The script analyzes the /var/log/auth.log file to:

Identify failed SSH login attempts.

Summarize failed attempts by IP address.

Automatically block malicious IPs using iptables.

Features

Log Analysis: Extracts failed SSH login attempts and identifies offending IP addresses.

Summary Output: Creates a detailed report of failed login attempts and a summary of offenders by IP address.

Automated Blocking: Dynamically blocks malicious IPs via iptables firewall rules.

Screenshots

Screenshots for each step of the process are included in the repository:

Running the script.

Log file analysis outputs.

IP summary generation.

Verifying the iptables rules.

Prerequisites

Ensure the following are installed and configured on your Linux machine:

Bash shell (default for most Linux distributions).

Access to /var/log/auth.log (requires sudo permissions).

iptables installed and configured.

Setup Instructions

Clone the repository:

git clone <repository-url>
cd <repository-folder>

Make the script executable:

chmod +x log_analysis.sh

Run the script with sudo:

sudo ./log_analysis.sh

Script Output

failed_attempts.txt****: Contains detailed information about each failed SSH login attempt, including:

Date and time.

Username.

IP address.

Example:

Dec 31 14:43:01 | User: fakeuser | IP: 192.168.78.129
Dec 31 14:44:21 | User: employee12 | IP: 192.168.78.129

ip_summary.txt****: Summarizes the failed attempts by IP address, listing the number of failed attempts per IP.

Example:

5 192.168.78.129

Firewall Action: Adds a rule to iptables to block the malicious IPs.
Example:

Chain INPUT (policy ACCEPT)
target     prot opt source               destination
DROP       all  --  192.168.78.129       0.0.0.0/0

Script Breakdown

Key Sections

Log File Path:

LOG_FILE="/var/log/auth.log"

Specifies the log file for analysis.

Failed Login Analysis:

grep "Failed password" "$LOG_FILE" | awk '{print $1, $2, $3, "| User:", $11, "| IP:", $13}' > "failed_attempts.txt"

Extracts and formats details about failed login attempts.

IP Summary Generation:

grep "Failed password" "$LOG_FILE" | awk '{print $13}' | sort | uniq -c | sort -nr > "ip_summary.txt"

Summarizes the number of failed attempts per IP.

IP Blocking:

while read COUNT IP; do
    if [ "$COUNT" -gt 3 ]; then
        iptables -A INPUT -s "$IP" -j DROP
        echo "Blocking IP: $IP with $COUNT failed attempts"
    fi
done < "ip_summary.txt"

Blocks IPs with more than 3 failed attempts.

Verification Steps

Verify the output files:

cat failed_attempts.txt
cat ip_summary.txt

Check iptables rules:

sudo iptables -L INPUT -v -n

Ensure malicious IPs are listed with the DROP rule.

Screenshots

Screenshots demonstrating each step are included in the screenshots/ directory.

License

This project is licensed under the MIT License. See LICENSE for details.


