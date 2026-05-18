---
tags: [basics, technique, windows]
---

[[Basics/Index|← Basics]]



```cmd
# Show current working directory 
echo %cd%

# Switching to D Drive 
D:

# Print out the content of a file 
type filename.txt

# List directory tree 
tree /F

# List directories 
dir 

# Which user am i?
whoami 

# Information about the computer name/hostname
hostname 
echo %COMPUTERNAME%

# IP configuration 
ipconfig /all

# List local user accounts 
net user 

# List local groups 
net localgroup

# List running tassks
tasklist 

# Show listening Ports 
netstat -ano | findstr LISTEN

# Listing all the drives 
wmic logicaldisk get name 



```
