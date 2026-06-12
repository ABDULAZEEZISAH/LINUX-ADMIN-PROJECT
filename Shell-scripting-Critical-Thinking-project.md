### Project Title: Automated Server Update and Web Server Installation Script

### Shell Script: Remote Server Update and Web Server Installation

**Project Goals:**

* Efficiency: Create a shell script that can efficiently update multiple remote servers.
* Automation: Automate the process of updating the servers to save time and reduce the risk of errors.
* Uniformity: Ensure that each server is configured consistently by installing both Apache2 and Nginx.
* Ease of Use: Design the script to be user-friendly and easy to execute.
* Error Handling: Implement error handling to address potential issues during the update and installation processes.

**Project Tasks:**

- Script Development: Write a shell script that incorporates the necessary commands for remote server access, system updates, and web server installation. You can use tools like SSH for remote access.
- Parameterization: Make the script flexible by allowing users to input server addresses or hostnames as parameters. This way, the same script can be used for different server configurations.
- Error Handling: Implement error checks and provide informative error messages to guide users on how to address common issues during the script's execution.
- Testing: Test the script on virtual machines or actual servers to ensure it functions as expected. Be prepared to troubleshoot and refine the script based on the testing results.
- Documentation: Create detailed documentation for users, including a user guide on how to run the script, input parameters, and interpret the output.

**Shell Script: Remote Server Update and Web Server Installation**

This script:

-  Connects to a remote Linux server using SSH.
- Verifies connectivity.
- Updates the operating system packages.
- Installs and starts Apache web server.
- Checks service status.
- Provides meaningful error messages.


# Check if required parameters are provided


if [ $# -ne 2 ]; then

echo "Usage: $0 <username> <server_ip_or_hostname>"

echo "Example: $0 ubuntu 192.168.56.10"

exit 1

fi

USERNAME=$1

SERVER=$2


echo "Remote Server Management Script"

echo "Target Server: $SERVER"

echo "User: $USERNAME"


# Verify server reachability

echo "[INFO] Checking connectivity..."

ping -c 2 $SERVER > /dev/null 2>&1

if [ $? -ne 0 ]; then

echo "[ERROR] Cannot reach server: $SERVER"

echo "Possible causes:"

echo "  - Server is offline"

echo "  - Incorrect hostname or IP address"

echo "  - Firewall restrictions"

exit 1

fi

echo "[SUCCESS] Server is reachable."

# Execute remote operations

ssh -o ConnectTimeout=10 ${USERNAME}@${SERVER} << 'EOF'

echo "[INFO] Connected successfully."

# Detect package manager

if command -v apt >/dev/null 2>&1; then

```
echo "[INFO] Ubuntu/Debian system detected."

sudo apt update -y

if [ $? -ne 0 ]; then

    echo "[ERROR] Package update failed."

    exit 1

fi

sudo apt upgrade -y

sudo apt install apache2 -y

sudo systemctl enable apache2

sudo systemctl start apache2

sudo systemctl status apache2 --no-pager


elif command -v dnf >/dev/null 2>&1; then


echo "[INFO] RHEL/Fedora system detected."

sudo dnf update -y

sudo dnf install httpd -y

sudo systemctl enable httpd

sudo systemctl start httpd

sudo systemctl status httpd --no-pager


elif command -v yum >/dev/null 2>&1; then


echo "[INFO] CentOS system detected."

sudo yum update -y

sudo yum install httpd -y

sudo systemctl enable httpd

sudo systemctl start httpd

sudo systemctl status httpd --no-pager


else
echo "[ERROR] Unsupported Linux distribution."
exit 1
fi

echo "[SUCCESS] Web server installation completed."

EOF

SSH_STATUS=$?

if [ $SSH_STATUS -ne 0 ]; then
echo "[ERROR] SSH connection failed."
echo "Verify:"
echo "  - SSH service is running on target server"
echo "  - Username is correct"
echo "  - SSH keys or password authentication are configured"
exit 1
fi

echo "======================================"
echo "[SUCCESS] All operations completed."
echo "======================================"
```

Test Environment

You can test using:

VirtualBox VMs

Successful Deployment
1. Start a Linux VM.
2. Enable SSH:
```
sudo systemctl enable ssh
sudo systemctl start ssh
```
3. Run
```
./server_setup.sh ubuntu 192.168.56.101
```


Expected Result:
```
[SUCCESS] Server is reachable.
[SUCCESS] Web server installation completed.
```



