<# Ansys2022R1Deployment
Deployment script for Ansys 2022 R1 when SCCM deployment fails

This script requires several things to correct Ansys' error

1. Admin credentials on the machines you are installing to
2. The installtion files for Ansys placed on a network share
3. A .bat file to install the software placed in the same network share folder as the setup.exe file (example file provided)
4. A CSV file containing the list of hostnames you wish to deploy Ansys to. Please note, CSV must use 'Name' as the column header for the hostnames
#>

$networkShare = # Please put in the path to your network share holding the installation files here
$csvPath = # Please enter the path to the CSV containing hostnames for the PCs here

$computers = Import-Csv -Path $csvPath

foreach ($computer in $computers) {

    $computerName = $computer.Name

    Write-Output "Current machine is " $computer

    $result = Test-Path -Path "\\$computerName\C$\Temp"

    if ($result -eq $true) {
    
        Write-Host $computerName -ForegroundColor Green

        Write-Host "C:\Temp detected, continuing execution"
    
    } else {
    
        Write-Host $computerName -ForegroundColor Red
        
        Write-Host "C:\Temp directory not detected. Creating directory..."

        New-Item \\$computerName\C$\Temp -ItemType directory -Force    

        Write-Host "C:\Temp directory created"
    }

    Remove-Item -Path "\\$computerName\C$\Temp\*" -Force -Recurse
    Write-Output "C:\Temp folder now empty"


	# Script copys the installation files from a network share, please replace $networkShare
    Write-Output "Downloading Ansys source files to C:\Temp"
    Copy-Item -Path $networkShare -Destination "\\$computerName\C$\Temp" -Recurse -Force
    Write-Output "Ansys source files downloaded"

    # The below Invoke-Command with the batch file works
    Invoke-Command -ComputerName $computerName -ScriptBlock { cmd.exe /c C:\Temp\install.bat }

    Write-Output $computerName " Complete"

}
