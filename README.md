# service-status
#Query current OS Service status
#This is a PowerShell exercise to determine the current status of the services on your computer or server prior performing a maintenance, #and export the status on a csv file for further reference after the computer or server has been patched.

#Get Current Services in "Running" Status & Export the list into a csv file
Get-Service | ?{$_.status -eq "Running"} | select name | export-csv c:\temp\RunningServices.csv -NoTypeInformation
#Get the service status from the source/original csv file
$ServiceList = Import-Csv "C:\Temp\RunningServices.csv"
Foreach ($s in $serviceList) {Start-service -name $s.name}
#Using a list of servers.
Foreach ($s in $serviceList) {Get-Service -ComputerName (Get-content ServerList.txt) -name $s.name}

#Obtain a list of service with "StartupType" set to Automatic and export them in the a csv list:
get-service | ?{$_.StartType -eq "Automatic"} | select name | export-csv c:\temp\AutomaticServices.csv -NoTypeInformation
$ServiceList = Import-Csv "C:\Temp\AutomaticServices.csv"
Foreach ($s in $ServiceList) {Set-service -name $s.name -StartupType Automatic}

#Excellent way to resolve the RU 18 issue setting the services to disable preventing successful Up Date RollUp 18
#http://blogs.catapultsystems.com/cmatthews/archive/2013/04/17/exchange-2010-upgrade-sets-all-exchange-services-to-disabled/
Instead of manually setting the services after each role upgrade, I decided to use powershell. I monitored the services, and when I saw the services become disabled, I ran the following Powershell command: 
Get-Service MSExchange* | Set-Service -Startuptype manual
Once the upgrade successfully completes, I ran the same command, but this time set the services to Automatic: 
Get-Service MSExchange* | Set-Service -Startuptype automatic
