Scenario: "Saint John": what is writing to this log file?

Level: Easy

Description: A developer created a testing program that is continuously writing to a log file /var/log/bad.log and filling up disk. You can check for example with tail -f /var/log/bad.log.
This program is no longer needed. Find it and terminate it. Do not delete the log file.

Test: The log file size doesn't change (within a time interval bigger than the rate of change of the log file).

The "Check My Solution" button runs the script /home/admin/agent/check.sh, which you can see and execute.

Time to Solve: 10 minutes.

OS: Debian 11

Root (sudo) Access: Yes



Solution:

To solve the "Saint John" scenario and stop the process filling up your disk, you need to identify which program is actively writing to /var/log/bad.log and then terminate it. 
Step-by-Step Solution
Identify the Process
Use the lsof (List Open Files) command to see which process has the file open for writing. Run this with sudo to ensure you see all system processes:
bash
sudo lsof /var/log/bad.log
Use code with caution.

Alternatively, you can use fuser to get the Process ID (PID) directly:
bash
sudo fuser /var/log/bad.log
Use code with caution.

Note the PID (Process ID) and the COMMAND name from the output.
Verify the Process (Optional)
To be sure you're killing the right thing, you can check the details of that PID:
bash
ps -fp <PID>
Use code with caution.

Terminate the Process
Once you have the PID, stop the program. Start with a standard termination signal:
bash
sudo kill <PID>
Use code with caution.

If it doesn't stop immediately (check with tail -f /var/log/bad.log to see if new lines are still appearing), use a forceful kill:
bash
sudo kill -9 <PID>
Use code with caution.

Verify the Solution
The file size should now remain constant. You can verify this by running the provided check script:
bash
sudo /home/admin/agent/check.sh
Use code with caution.

 
Quick Command Summary
If you want to do it in one go (assuming fuser is installed):
bash
sudo fuser -k /var/log/bad.log