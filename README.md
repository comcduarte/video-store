# EXPORT & ARCHIVE OF CAMERA FOOTAGE

## PREREQUISTIES
List of software required to perform operations:
1.	@boxcli [https://github.com/box/boxcli] 
2.	MilestonePSTools [https://www.milestonepstools.com/getting-started/#installation]
3.	Mkvtoolnix [https://mkvtoolnix.org/]

## ENVIRONMENT
Chose to install this process on a dedicated server.  This server requires internet access.  The above applications are installed on the server.
A scheduled task was created to upload the camera data, one day in arears, to a box shared folder available to the public.

## SCRIPT	
PowerShell is used to script the scheduled task.  Below is a brief example of the commands that can be performed manually.
```
# Get ID for camera
$camera = Get-VmsCamera | Where-Object -Property Name -Like "Cam*"

# Export to a temporary file.  Make sure the drive you save this to
# has enough free space.
Start-Export -CameraIds $camera.id -Format MKV -StartTime '2025-01-01 00:00:00' -EndTime '2025-01-01 23:59:59' -Path E:\BOX\MILESTONE -Name "TEMP"

# split the file into 1G chunks.  @boxcli does not allow for large file
# uploads
mkvmerge.exe --split 1G --output E:\BOX\MILESTONE\2025-01-07.mkv E:\BOX\MILESTONE\temp.mkv

# Sends files to specified Box folder by FolderID
Send-BoxFiles.ps1 -Path E:\BOX\MILESTONE -FolderID 123456789012345

# Remove Temporary File
Remove-Item –File TEMP.mkv
```

## CAVEATS
This system uses the MKV file format, which is much more efficient.  Especially during the export.  It takes significantly less time and space.  MKV files, if not supported with native software, can easily be viewed with VLC Player.
