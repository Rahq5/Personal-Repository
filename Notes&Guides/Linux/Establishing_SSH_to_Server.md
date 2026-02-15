# How to establish SSH connection
### First time (installation)

in this guide i used my primary laptop (ubuntu) and another linux laptop (mint).

After connecting you will notice that your user name are switched to server's username like your are opening the server's terminal but remotely.
```bash
JackRipper2004@LinuxUser:~$ ssh CanonicalServer@192.168.10.291

CanonicalServer@Something:~$ 
```


**Step 1: Install SSH on both laptops**
**On Mint laptop (the one you want to connect TO):**
```bash
sudo apt update
sudo apt install openssh-server
```

**On Ubuntu laptop (the one you connect FROM):**
```bash
sudo apt install openssh-client
```

**Step 2: Start SSH service on Mint laptop**
```bash
sudo systemctl start ssh
sudo systemctl enable ssh  # Auto-start on boot
```

Check if running:
```bash
sudo systemctl status ssh
```

**Step 3: Find Mint laptop's IP address**
On Mint laptop:
```bash
ip a
```

Look for IP like `192.168.x.x` under `wlan0` (WiFi) or `eth0` (Ethernet)
Or simpler:
```bash
hostname -I
```

**Step 4: Connect from Ubuntu laptop**
```bash
ssh username@mint-ip-address
```
Example:
```bash
ssh john@192.168.1.100
```
First time asks to verify fingerprint, type `yes`
Enter password when prompted.

**Step 5: (Optional) Setup passwordless login**
On Ubuntu laptop, generate SSH key:
```bash
ssh-keygen -t ed25519
```
Press Enter 3 times (default location, no passphrase)
Copy key to Mint laptop:
```bash
ssh-copy-id username@mint-ip-address
```
Now you can SSH without password.
### Further times (Recent Connections)

if you have connected to the same server before and did installed system required you only gonna need few things:
1. username of server (i mean the linux username)
2. IP address
3. Server's password 

**Process:**
1. connect using `SSH` Command
```
SSH <username>@<IP-Address>
```
example
```
SSH JackRipper2004@192.168.9.292
```

2. enter password of server and your are in !

## Some things to do while in connection
### **Basic Operations After SSH Connection:**
**1. Navigate and explore**
```bash
pwd                    # Current directory
ls -la                 # List files
cd /path/to/folder     # Change directory
```

**2. File operations**
```bash
# Create/edit files
nano filename.txt      # Open text editor
mkdir new_folder       # Create directory
rm -rf folder_name     # Delete directory
cp file1 file2         # Copy file
mv old new             # Move/rename
```

**3. View files**
```bash
cat file.txt           # Show entire file
less file.txt          # Scroll through file (q to quit)
head -n 10 file.txt    # First 10 lines
tail -n 10 file.txt    # Last 10 lines
```

**4. System information**
```bash
df -h                  # Disk space
free -h                # RAM usage
top                    # Running processes (q to quit)
uname -a               # System info
```

**5. Install/manage software**
```bash
sudo apt update
sudo apt install package-name
sudo apt remove package-name
```

**6. Process management**
```bash
ps aux                 # All running processes
kill PID               # Stop process by ID
killall process-name   # Stop by name
```

**7. Exit SSH**
```bash
exit
# or press Ctrl+D
```

### **Transfer Files (run from Ubuntu laptop, NOT in SSH):**

**Copy file TO Mint laptop:**
```bash
scp /local/file.txt username@mint-ip:/remote/path/
```

**Copy file FROM Mint laptop:**
```bash
scp username@mint-ip:/remote/file.txt /local/path/
```

**Copy entire folder:**
```bash
scp -r /local/folder username@mint-ip:/remote/path/
```

**Better alternative (shows progress):**
```bash
rsync -avzP /local/folder/ username@mint-ip:/remote/path/
```

### **Run GUI applications (display on Ubuntu):**

First, connect with X11 forwarding:
```bash
ssh -X username@mint-ip
```
Then run GUI apps:
```bash
firefox &              # Opens Firefox on your Ubuntu screen
gedit file.txt &       # Text editor
```


### **Keep processes running after disconnect:**
Install screen:
```bash
sudo apt install screen
```
Start a screen session:
```bash
screen
# run your command
# Press Ctrl+A then D to detach
```
Reconnect later:
```bash
screen -r
```