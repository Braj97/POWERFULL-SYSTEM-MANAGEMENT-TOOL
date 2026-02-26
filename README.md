# POWERFULL-SYSTEM-MANAGEMENT-TOOL
POWERFULL SHELL
# ===============================================
#        ADVANCED SYSTEM MANAGEMENT TOOL
#        Author: Braj Kishor Das
# ===============================================

Clear-Host

# Global Log File
$LogFile = "$env:USERPROFILE\Desktop\System_Tool_Log.txt"

function Write-Log {
    param ([string]$Message)
    $TimeStamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Add-Content -Path $LogFile -Value "$TimeStamp - $Message"
}

function Show-Header {
    Clear-Host
    Write-Host "=========================================" -ForegroundColor Cyan
    Write-Host "     ADVANCED SYSTEM MANAGEMENT TOOL     " -ForegroundColor Yellow
    Write-Host "=========================================" -ForegroundColor Cyan
}

# 1️⃣ SYSTEM INFORMATION
function Get-SystemInfo {
    Show-Header
    Write-Host "System Information" -ForegroundColor Green
    Write-Host "-----------------------------------------"

    $os = Get-WmiObject Win32_OperatingSystem
    $cpu = Get-WmiObject Win32_Processor
    $ram = Get-WmiObject Win32_ComputerSystem

    Write-Host "OS Name: $($os.Caption)"
    Write-Host "Version: $($os.Version)"
    Write-Host "CPU: $($cpu.Name)"
    Write-Host "RAM (GB): {0:N2}" -f ($ram.TotalPhysicalMemory / 1GB)

    Write-Log "Viewed System Information"
    Pause
}

# 2️⃣ PROCESS MANAGEMENT
function Manage-Processes {
    Show-Header
    Write-Host "Running Processes" -ForegroundColor Green
    Get-Process | Sort-Object CPU -Descending | Select-Object -First 10

    $choice = Read-Host "Do you want to stop a process? (yes/no)"
    if ($choice -eq "yes") {
        $procName = Read-Host "Enter Process Name"
        Stop-Process -Name $procName -Force -ErrorAction SilentlyContinue
        Write-Host "Process stopped (if existed)." -ForegroundColor Red
        Write-Log "Stopped Process: $procName"
    }
    Pause
}

# 3️⃣ FILE MANAGEMENT
function File-Manager {
    Show-Header
    Write-Host "FILE MANAGEMENT SYSTEM" -ForegroundColor Green

    Write-Host "1. Create File"
    Write-Host "2. Delete File"
    Write-Host "3. Read File"
    Write-Host "4. Copy File"

    $opt = Read-Host "Select option"

    switch ($opt) {
        1 {
            $path = Read-Host "Enter Full File Path"
            New-Item -Path $path -ItemType File
            Write-Log "Created File: $path"
        }
        2 {
            $path = Read-Host "Enter Full File Path"
            Remove-Item $path -Force
            Write-Log "Deleted File: $path"
        }
        3 {
            $path = Read-Host "Enter Full File Path"
            Get-Content $path
            Write-Log "Read File: $path"
        }
        4 {
            $source = Read-Host "Enter Source Path"
            $dest = Read-Host "Enter Destination Path"
            Copy-Item $source $dest
            Write-Log "Copied File from $source to $dest"
        }
    }
    Pause
}

# 4️⃣ USER ACCOUNT MANAGEMENT
function User-Management {
    Show-Header
    Write-Host "USER MANAGEMENT" -ForegroundColor Green

    Write-Host "1. List Local Users"
    Write-Host "2. Create Local User"
    Write-Host "3. Delete Local User"

    $choice = Read-Host "Select option"

    switch ($choice) {
        1 {
            Get-LocalUser
            Write-Log "Listed Users"
        }
        2 {
            $username = Read-Host "Enter Username"
            $password = Read-Host "Enter Password" -AsSecureString
            New-LocalUser -Name $username -Password $password
            Write-Log "Created User: $username"
        }
        3 {
            $username = Read-Host "Enter Username"
            Remove-LocalUser -Name $username
            Write-Log "Deleted User: $username"
        }
    }
    Pause
}

# 5️⃣ NETWORK INFORMATION
function Network-Info {
    Show-Header
    Write-Host "NETWORK INFORMATION" -ForegroundColor Green

    Get-NetIPAddress
    Get-NetAdapter

    Write-Log "Viewed Network Info"
    Pause
}

# 6️⃣ DISK CLEANUP
function Disk-Cleanup {
    Show-Header
    Write-Host "Performing Temporary File Cleanup..." -ForegroundColor Green

    $tempPath = "$env:TEMP\*"
    Remove-Item $tempPath -Recurse -Force -ErrorAction SilentlyContinue

    Write-Host "Temporary Files Deleted." -ForegroundColor Yellow
    Write-Log "Disk Cleanup Performed"
    Pause
}

# 7️⃣ MAIN MENU LOOP
do {
    Show-Header
    Write-Host "1. System Information"
    Write-Host "2. Process Management"
    Write-Host "3. File Manager"
    Write-Host "4. User Management"
    Write-Host "5. Network Information"
    Write-Host "6. Disk Cleanup"
    Write-Host "7. Exit"

    $menu = Read-Host "Enter Your Choice"

    switch ($menu) {
        1 { Get-SystemInfo }
        2 { Manage-Processes }
        3 { File-Manager }
        4 { User-Management }
        5 { Network-Info }
        6 { Disk-Cleanup }
        7 { Write-Host "Exiting..."; break }
        default { Write-Host "Invalid Option" }
    }

} while ($menu -ne 7)

Write-Log "Program Closed"
