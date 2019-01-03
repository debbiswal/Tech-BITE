[Home](https://debbiswal.github.io/Tech-BITE) \| [Back](https://debbiswal.github.io/Tech-BITE/#redis)

## Get Date & Time stamp of Redis using Powershell  

Lets say you have many Redis servers in which Redis is running on 10001 port.  
We want to get the Date & Time from each Redis instance from Windows .  

Here is a sample program in Powershell , which pings each Redis instance.  
The Redis server list is read from server_list.txt file.  

Sample code :  
```powershell
function PrintDateTime
{
  Param ([string]$redis_host,[int]$redis_port)  
  try
  {
    $client = New-Object Net.Sockets.TcpClient($redis_host,$redis_port)
    $stream = $client.GetStream()
    $bytes = [Text.Encoding]::ASCII.GetBytes("TIME`r`n")
    $stream.Write($bytes, 0, $bytes.Length)
    $buffer = New-Object byte[] 32
    $read = $stream.Read($buffer, 0, 32)
    $response = [Text.Encoding]::ASCII.GetChars($buffer) -join ''
    [String[]] $arrLines = ($response -split "\r\n")
    $unixTime = $arrLines[2]
     
    $origin = New-Object -Type DateTime -ArgumentList 1970, 1, 1, 0, 0, 0, 0
    $timestamp = $origin.AddSeconds($unixTime)
    
    Write-Host -NoNewline "$redis_host" -foregroundcolor "Green"
    Write-Host " => $timestamp" -foregroundcolor "Yellow"
    Write-Host " "
        
    $client.Close()
  }
  Catch [System.Management.Automation.MethodInvocationException]
   {
       Write-Host "Unable to connect $redis_host $redis_port" -foregroundcolor "Green"
   }
  Catch
   {
     $ErrorMessage = $_.Exception.Message
     $FailedItem = $_.Exception.ItemName 
     Write-Host "$ErrorMessage , $FailedItem" -foregroundcolor "Green"
   }
   return $status
}


$serverList = Get-Content D:\REDIS\Redis_timestamp\server_list.txt

Write-Host " "
Write-Host "              REDIS SERVER CURRENT DATE & TIME " -foregroundcolor "GREEN"
Write-Host "=========================================================" -foregroundcolor "WHITE"

$count =1
ForEach ($server in $serverList) {
    PrintDateTime $server 10001
}

Write-Host ""
Write-Host "Press any key to exit ..." -foregroundcolor "Blue"
$x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")
```  

Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE) \| [Back](https://debbiswal.github.io/Tech-BITE/#redis)
