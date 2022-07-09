---
id: demt2mwnkzcmp7pylxenbst
title: SP1 - Correct bad playlists
desc: ''
updated: 1657295686648
created: 1657294672432
---
# Initial Notes
While updating the database within MediaMonkey, noticed that quite a few playlists within the Music Library did not show any content. Examination of a few showed that absolute paths had been set when the playlist files were created and that removing these paths to just leave the filename, repaired this defect.
To see the size of the problem ran

```$ grep --include=*.m3u -Rl . -e "Library" > bad_m3u_files.txt```

from within the Music Library root, and this identified more than 1,900 bad M3U playlist files.
To correct this, decided to create a Perl script to edit and correct the bad files:

- For each M3U file, open and read in the contents
- For each line, check if contains a marker, eg "Library"
- If yes, process the line to remove the path
- Write corrected file contents, and close the file

To find the M3U files in the Music Library folder structure, the preferable route would be to move down through the tree and examine each file as it is found. Not sure how to do this in Perl, but starting with the chdir function and collecting any M3U files in the new folder for processing, and collecting any sub-folders in a list for further examination, might work. If this is too big a task, could run the grep command above and then present the bad_m3u_files.txt as the files to be processed. In any case, need to log changes made for quality control. To prevent disastrous changes, need to backup the Music Library before running in anger. Might be worth having an option in the script to carry out a dry-run so that the changes can be reviewed before they are made.

# Project Scope / Requirements
To create a software tool or script that will correct the absolute path problems in the playlist files. This tool will be used on more then one occasion, when required. Playlist files that do not exhibit the problems this tool corrects must remain unchanged.
The software must:

- Start work in the top level folder of the Music file library
- Scan down through the folder structure
- Open each playlist file it finds
- Check each line in the file for an absolute path
- If an absolute path is found, the path needs to be removed
- If there is no absolute path, the line is left unchanged
- On reaching the end of the file, the original file is saved under a different name and the corrected file is written to replace the original file
- Record all decisions and changes made to a log file

The software will be written in a scripting language like Perl or Python, with the intention of running the software on a Linux system, as Linux normally has Perl and Python interpreters already installed.
The software development process might use the GitHub system for version control and development issues (which also has access to ZenHub for project management). The GitHub system works best with text files and might also be useful for safe storage of documentation. Use of the yEd drawing tool could be used for diagrams as it saves to graphxml format. Markdown could be used for the actual documentation. It is intended that the development processes be carried out on a Linux system.

Proposed name for tool is **m3u-abs2rel**.

Future development could include a "reverse" correction, where an absolute path is required to be placed at the head of each line, ie a **m3u-rel2abs** tool, with perhaps a wrapper tool around both, **m3u-change**.

# Project Planning and Development
Deciding that the tool would be written in Perl required a period of self-learning with SAMS Teach Yourself Perl in 24 Hours. This was done in c:\devs\devprojects\perldevs\Perl24, an area that is synced with MEGA between machines. The book ended up being used more as a reference guide, but helped a lot with the required functionality for this software tool. An iterative approach was used to develop the tool using test input and output pulled out of the Music Library.

As the functionality in this script is relatively basic with no mathematics involved, it was decided that debugging levels were not required and that checking could be done via the log file.

For file handling, the **File::Find** module was included which allowed the script to progress through a folder tree, searching for the playlist files to be edited. The root folder for the Music Library is supplied via the command line, along with a second parameter which allows a dry-run to be completed to test the file changes that would be carried out before actually changing any files. As an extra safety feature, all changed files are backed up before the originals are overwritten. The backup file is written to the same folder as the original.
Once a playlist file has been found and opened, each line is examined to see if it contains an absolute path and, if so, the path is removed and the line saved. Records of the changes are saved in a log file for later examination. This log can also be produced with a "dry-run" so that changes can be checked before they are carried out.

# Project Completion
Version 1.0 of **m3u-abs2rel.pl** was finished on 16 June 2019. This has been stored in c:\devs\devprojects\perldevs which is automatically synced via MEGA. As the tool requires run-time arguments, a batch file (**m3u-abs2rel.bat**)has been created to run this tool. The batch file is stored in OneDrive\bin which has been added to the computer's path environment. Executing the batch file (double-clicking in the folder or at the command line) calls the Perl script with the batch supplied arguments and the log file will be written to the folder where the execution took place.
(
)
(Development of Version 1.1)
(
)
(The current version does not allow for retaining partial paths, for instance, for a multi-disk album. Need to change the code where the end of the path is identified in the filename).
