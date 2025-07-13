# Linux-Backs-up-any-encrypted-password-files-that-have-been-updated-in-the-past-24-hours.

📄 Project Title: Automated Backup Script (backup.sh)
🧑‍💻 Author: [Souvik Mandal]
🏢 Organization: ABC International Inc.
📅 Project Goal:
To automate the process of backing up encrypted password files modified in the past 24 hours, reducing human error and improving efficiency.

📜 Script Breakdown and Explanation
bash
Copy
Edit
#!/bin/bash
Shebang – Specifies the interpreter to execute the script, in this case /bin/bash.

bash
Copy
Edit
if [[ $# != 2 ]]
then
  echo "backup.sh target_directory_name destination_directory_name"
  exit
fi
Task: Validate that exactly two arguments are passed to the script.

$# holds the number of passed arguments.

If not 2, show usage instruction and exit.

bash
Copy
Edit
if [[ ! -d $1 ]] || [[ ! -d $2 ]]
then
  echo "Invalid directory path provided"
  exit
fi
Task: Validate that both passed arguments are valid directories.

-d checks if the path is a directory.

bash
Copy
Edit
targetDirectory=$1
destinationDirectory=$2
Task 1: Assign the first and second command-line arguments to respective variables.

bash
Copy
Edit
echo "Target Directory: $targetDirectory"
echo "Destination Directory: $destinationDirectory"
Task 2: Print the provided directories for confirmation.

bash
Copy
Edit
currentTS=$(date +%s)
Task 3: Capture the current timestamp (in seconds since Epoch) into currentTS.

bash
Copy
Edit
backupFileName="backup-$currentTS.tar.gz"
Task 4: Set the name of the backup file using the timestamp.

bash
Copy
Edit
origAbsPath=$(pwd)
Task 5: Save the absolute path of the original directory (before any cd).

bash
Copy
Edit
cd "$destinationDirectory" || exit
destDirAbsPath=$(pwd)
Task 6: Change to destination directory and get its absolute path.
|| exit ensures script exits if cd fails.

bash
Copy
Edit
cd "$origAbsPath" || exit
cd "$targetDirectory" || exit
Task 7: Navigate to the target directory where files will be scanned.

bash
Copy
Edit
yesterdayTS=$((currentTS - 24 * 60 * 60))
Task 8: Calculate the timestamp 24 hours ago for comparison.

bash
Copy
Edit
declare -a toBackup
Initialize an array toBackup to store names of files to archive.

bash
Copy
Edit
for file in *
Task 9: Loop through all files and directories in the current folder.

bash
Copy
Edit
if [[ $(date -r "$file" +%s) -gt $yesterdayTS ]]
Task 10: Check if file's last modified time is within the last 24 hours.

bash
Copy
Edit
toBackup+=("$file")
Task 11: Add qualified file to the toBackup array.

bash
Copy
Edit
tar -czvf $backupFileName ${toBackup[@]}
Task 12: Archive and compress all files in the toBackup array into one .tar.gz file.

bash
Copy
Edit
mv $backupFileName $destDirAbsPath
Task 13: Move the backup file to the destination directory.

✅ Output Example
When run correctly:

bash
Copy
Edit
Target Directory: /home/user/docs
Destination Directory: /home/user/backups
backup-1718892304.tar.gz
🕒 Crontab Example
To run this script daily:

bash
Copy
Edit
0 0 * * * /usr/local/bin/backup.sh /path/to/source /path/to/destination
To run every 1 minute for testing:

bash
Copy
Edit
*/1 * * * * /usr/local/bin/backup.sh /path/to/source /path/to/destination
