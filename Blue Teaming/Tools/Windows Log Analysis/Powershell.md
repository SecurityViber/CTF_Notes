
## Useful PowerShell Queries

```powershell
# Get failed logon events
Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625} | Select-Object TimeCreated, Message | Format-List

# Get all logon events in the last 24 hours
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624; StartTime=(Get-Date).AddDays(-1)}

# Search for a specific event ID across all logs
Get-WinEvent -ListLog * | Where-Object {$_.RecordCount -gt 0} | 
  ForEach-Object { Get-WinEvent -LogName $_.LogName -ErrorAction SilentlyContinue | 
    Where-Object {$_.Id -eq 4698} }

# Export to XML for analysis
wevtutil epl Security C:\security.evtx
```
