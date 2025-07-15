📂 backup.sh Script Explanation
The backup.sh script is a Bash-based automation tool that backs up files modified in the last 24 hours. It enhances security, removes manual effort, and supports scheduling via cron. Here's a step-by-step explanation:

🧠 Script Breakdown
✅ Input Validation

```
if [[ $# != 2 ]]
then
  echo "backup.sh target_directory_name destination_directory_name"
  exit
fi
```
Ensures exactly two arguments are passed (target and destination directories); otherwise, it exits with an error.

📁 Directory Verification
```
if [[ ! -d $1 ]] || [[ ! -d $2 ]]
then
  echo "Invalid directory path provided"
  exit
fi
```
Checks if both arguments are valid existing directories.

🏷 Assign Variables
```
targetDirectory=$1
destinationDirectory=$2
```
Stores input arguments in readable variable names.

📢 Display Paths
```
echo "Target Directory: $targetDirectory"
echo "Destination Directory: $destinationDirectory"
```
Prints the source and destination paths to the terminal for confirmation.

⏱ Get Current Timestamp
```
currentTS=$(date +%s)
```
Captures the current UNIX timestamp to uniquely name the backup file.

💾 Set Backup Filename
```
backupFileName="backup-$currentTS.tar.gz"
```
Defines the name of the backup archive using the current timestamp.

📍 Save Original Directory
```
origAbsPath=$(pwd)
```
Saves the current working directory path.

🚚 Navigate to Destination Directory
```
cd "$destinationDirectory" || exit
destDirAbsPath=$(pwd)
```
Moves to the destination directory and stores its absolute path.

↩️ Go Back and Enter Target Directory
```
cd "$origAbsPath" || exit
cd "$targetDirectory" || exit
```
Returns to the original directory and then navigates to the target directory.

⏳ Calculate Timestamp for 24 Hours Ago
```
yesterdayTS=$((currentTS - 24 * 60 * 60))
```
Calculates the timestamp for files modified within the last 24 hours.

🧮 Initialize Array
```
declare -a toBackup
```
Declares an empty array to store the list of files to back up.

🔍 Find Modified Files
```
for file in *
do
  if [[ $(date -r "$file" +%s) -gt $yesterdayTS ]]
  then
    toBackup+=("$file")
  fi
done
```
Loops over all files in the directory and adds the recently modified ones to the array.

📦 Create Backup Archive
```
tar -czvf $backupFileName ${toBackup[@]}
```
Creates a .tar.gz compressed archive with the selected files.

📤 Move Backup File
```
mv $backupFileName $destDirAbsPath
```
Transfers the archive to the specified destination directory.

⏰ Schedule Daily Backup with Cron
You can automate this script to run every day using crontab -e:

```
0 0 * * * /usr/local/bin/backup.sh /path/to/source /path/to/destination
```
This runs the script daily at midnight.

📁 Example Directory Structure
```
project/
├── backup.sh
├── source/
│   ├── file1.txt
│   └── file2.log
└── destination/
    └── backup-1721025600.tar.gz
```
🚀 Usage
```
./backup.sh /home/user/data /home/user/backups
```
Backs up files modified in the last 24 hours from /home/user/data to /home/user/backups.
