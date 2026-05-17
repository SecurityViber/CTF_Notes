
Here some comman commands you can use to work with Processes 

```sh 
# Greping for a process 
ps aux | grep -i <some_string> 

# Using pgrep for cleaner output 
pgrep -a <some_string> 

# Get the id of a process 
pidof <process_name> 

# Kill a process by id 
sudo kill <pid> 

# Force kill a process by id 
sudo kill -9 <pid> 

# Kill a process by name 
sudo pkill <process_name> 


```