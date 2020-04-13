LibTar
======

Native ObjectPascal Library for accessing **tar** (*T*ape *Ar*chive)
files

Author: Stefan Heymann, E-Mail
[stefan@destructor.de](mailto:stefan@destructor.de)

Purpose
-------

LibTar is a Library which contains the ObjectPascal class
**TTarArchive.** Using this class, you can read the contents (Directory
entries and file contents) of a tar file.

About *tar*
-----------

tar is a format widely used on Unix and Linux systems. "tar" is short
for "Tape Archive". The format was intended primarily for use with tape
backup devices. However, tar files are also stored on disks. The common
extension for tar files is ".tar".

A tar file contains several files, making one file out of them. There is
no compression. The tar file just contains a directory entry for each
file (which holds information like the name of the file, size, last
modified date, access permissions, etc.). and the file itself.

TTarArchive
-----------

Using the TTarArchive class, you scan through the tar file, thereby
reading out the directory entries and – if you want – the files.

+--------------------------------------+--------------------------------------+
| **Step**                             | **Code**                             |
+--------------------------------------+--------------------------------------+
| **Choose a constructor**             |     constructor Create (Stream : TSt |
| You can either pass a TStream        | ream);  overload;                    |
| instance or a filename               |     constructor Create (Filename : S |
|                                      | TRING); overload;                    |
+--------------------------------------+--------------------------------------+
| **Make an instance of TTarArchive**  |     TA := TTarArchive.Create (Filena |
|                                      | me);                                 |
+--------------------------------------+--------------------------------------+
| **Prepare your scan**                |     TA.Reset;                        |
+--------------------------------------+--------------------------------------+
| **Scan through the archive**         |     while TA.FindNext (DirRec) do be |
|                                      | gin                                  |
+--------------------------------------+--------------------------------------+
| **Evaluate the DirRec for each       |       ListBox.Items.Add (DirRec.Name |
| file**                               | );                                   |
+--------------------------------------+--------------------------------------+
| **Read out the current file**        |       TA.ReadFile (DestFilename);    |
| You can leave this step out if you   |       END;                           |
| don't want to read the file          |                                      |
+--------------------------------------+--------------------------------------+
| **You're done**                      |     TA.Free                          |
+--------------------------------------+--------------------------------------+

FindNext, TTarDirRec
--------------------

The **FindNext** function returns two values:

The function return value (boolean) is true, when there was another file
found in the archive and false when the end of the archive has been
reached. So you can use it in a "while FindNext () do" loop. Using this
loop, you hop from file to file, accessing one at a time.

There is a var parameter, named DirRec of type TTarDirRec. This record
gives all the informations about the current file in the archive has the
following fields:

+--------------------------------------+--------------------------------------+
| **Field Name**                       | **Content**                          |
+--------------------------------------+--------------------------------------+
| **Name**                             | The file's path and file name. The   |
|                                      | path is usually divided with slash   |
|                                      | characters, because tar is a Unix    |
|                                      | format                               |
+--------------------------------------+--------------------------------------+
| **Size**                             | The size of the file in bytes        |
+--------------------------------------+--------------------------------------+
| **DateTime**                         | Last last modification date and      |
|                                      | time. Note that this time is given   |
|                                      | in UTC (GMT). You must probably      |
|                                      | convert it to your local time before |
|                                      | using it for comparisons.            |
+--------------------------------------+--------------------------------------+
| **Permissions**                      | A set telling who had which access   |
|                                      | rights to the file at the time it    |
|                                      | was written to the archive:          |
|                                      |   ------------------- -------------- |
|                                      | ------------------------------------ |
|                                      | --------------                       |
|                                      |   tpReadByOwner,\     The file owner |
|                                      |  has permission to read/write/execut |
|                                      | e it                                 |
|                                      |    tpWriteByOwner,\                  |
|                                      |    tpExecuteByOwner                  |
|                                      |                                      |
|                                      |   tpReadByGroup,\     The user's gro |
|                                      | up has permission to read/write/exec |
|                                      | ute the file                         |
|                                      |    tpWriteByGroup,\                  |
|                                      |    tpExecuteByGroup                  |
|                                      |                                      |
|                                      |   tpReadByOther,\     All other user |
|                                      | s have permission to read/write/exec |
|                                      | ute the file                         |
|                                      |    tpWriteByOther,\                  |
|                                      |    tpExecuteByOther                  |
|                                      |   ------------------- -------------- |
|                                      | ------------------------------------ |
|                                      | --------------                       |
+--------------------------------------+--------------------------------------+
| **FileType**                         | The type of the file:                |
|                                      |   ---------------------- ----------- |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ---------------                      |
|                                      |   **TFileType**          **Descripti |
|                                      | on**                                 |
|                                      |   ftNormal               A regular f |
|                                      | ile                                  |
|                                      |   ftLink                 A link to a |
|                                      | nother, previously archived, file. T |
|                                      | here is no data stored for a link. T |
|                                      | he name of the file which the link r |
|                                      | efers to is stored in the "LinkName" |
|                                      |  field of TTarDirRec                 |
|                                      |   ftSymbolicLink         A symbolic  |
|                                      | link to another file. There is no da |
|                                      | ta stored for a link. The name of th |
|                                      | e file which the link refers to is s |
|                                      | tored in the "LinkName" field of TTa |
|                                      | rDirRec                              |
|                                      |   ftCharacter, ftBlock   Character/B |
|                                      | lock special files. Unix specific. C |
|                                      | an be treated as a regular file by o |
|                                      | ther OSes                            |
|                                      |   ftDirectory            A subdirect |
|                                      | ory entry. There is no data stored f |
|                                      | or a directory. The "Size" field of  |
|                                      | TTarDirRec is zero (unlimited) or gi |
|                                      | ves the maximum number of bytes whic |
|                                      | h may be stored in the directory.    |
|                                      |   ftFifo                 FIFO specia |
|                                      | l file. There is no data stored for  |
|                                      | a FIFO file.                         |
|                                      |   ftContiguous           If supporte |
|                                      | d by the OS, the file shall be store |
|                                      | d in contiguous blocks of the storag |
|                                      | e medium. Otherwise, it can be treat |
|                                      | ed like a regular file.              |
|                                      |   ftDumpDir              This repres |
|                                      | ents a directory and a list of files |
|                                      |  created by the -G option of tar. Th |
|                                      | e Size field gives the total size of |
|                                      |  the associated list of files. Each  |
|                                      | filename is preceded by either a 'Y' |
|                                      |  (meaning the file should be in this |
|                                      |  archive) or an 'N' (The file is a d |
|                                      | irectory, or is not stored in the ar |
|                                      | chive). Each filename is terminated  |
|                                      | by a null. There is an additional nu |
|                                      | ll after the last filename.          |
|                                      |   ftMultivolume          This repres |
|                                      | ents a file continued from another v |
|                                      | olume of a multi-volume archive crea |
|                                      | ted with the -M option of tar. The o |
|                                      | riginal type of the file is not give |
|                                      | n here. Size field gives the maximum |
|                                      |  size of this piece of the file (ass |
|                                      | uming the volume does not end before |
|                                      |  the file is written out). The offse |
|                                      | t field gives the offset from the be |
|                                      | ginning of the file where this part  |
|                                      | of the file begins. Thus size plus o |
|                                      | ffset should equal the original size |
|                                      |  of the file.                        |
|                                      |   ftVolumeHeader         This file t |
|                                      | ype is used to mark the volume heade |
|                                      | r that was given with the -V option  |
|                                      | when the archive was created. The Na |
|                                      | me field contains the name given aft |
|                                      | er the -V option. The Size field is  |
|                                      | zero. Only the first file in each vo |
|                                      | lume of an archive should have this  |
|                                      | type.                                |
|                                      |   ---------------------- ----------- |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ------------------------------------ |
|                                      | ---------------                      |
+--------------------------------------+--------------------------------------+
| **LinkName**                         | If the FileType is ftLink or         |
|                                      | ftSymbolicLink, the LinkName field   |
|                                      | contains the name of the file where  |
|                                      | the link is going to.                |
+--------------------------------------+--------------------------------------+
| **UID, GID**                         | Numerical User ID and Group ID       |
+--------------------------------------+--------------------------------------+
| **UserName, GroupName**              | User Name and Group Name             |
+--------------------------------------+--------------------------------------+
| **CheckSumOK**                       | True, if the checksum of the tar     |
|                                      | header was checked OK, false if not. |
+--------------------------------------+--------------------------------------+
| **Mode**                             | I don't know enough about tar to     |
|                                      | tell you what the heck this means.   |
|                                      | Sorry.                               |
+--------------------------------------+--------------------------------------+
| **Magic**                            | Contains the string in the "magic"   |
|                                      | field of the tar header. Possible    |
|                                      | values are 'ustar' and 'GNUtar'. If  |
|                                      | it is 'ustar', then the UserName and |
|                                      | GroupName field can be considered    |
|                                      | valid. If it is 'GNUtar' then there  |
|                                      | is a GNU format dump entry.          |
+--------------------------------------+--------------------------------------+
| **MajorDevNo, MinorDevNo**           | Major and Minor Device Number for    |
|                                      | Files of type ftCharacter or ftBlock |
+--------------------------------------+--------------------------------------+
| **FilePos**                          | The Position in the tar file. This   |
|                                      | corresponds to the byte position of  |
|                                      | the first byte of the current header |
|                                      | record. You can use the "SetFilePos" |
|                                      | method to return to this position at |
|                                      | any time later.                      |
+--------------------------------------+--------------------------------------+

File Position
-------------

There are several methods you can use to retrieve or set the Current
Position in the tar file during or after scanning:

-   Every *TTarDirRec* contains a field *FilePos* which contains the
    Current Position of the beginning of the corresponding tar header
    record.
-   You can call the *GetFilePos* method at any time to retrieve the
    Current Position (Current) and the size (Size) of the whole tar
    file. Both values are given in bytes. You could use these values for
    display of a progress bar. The percentage is calculated:\
     `Percent := Current * 100 DIV Size;`
-   You can return to a position given by *TTarDirRec.FilePos* or
    *GetFilePos* with the *SetFilePos* method. After the call to
    *SetFilePos,* you must call *FindNext* to re-read the Directory
    record entry. After the call to *FindNext,* you can read the file
    contents using a call to *ReadFile.*

Source, Legals ("Licence")
--------------------------

The official site to get this code is
[http://www.destructor.de](http://www.destructor.de)\
 \
 Usage and Distribution of this Source Code is ruled by the
"Destructor.de Source code Licence" (DSL) which comes with this file or
can be downloaded at http://www.destructor.de\
 \
 IN SHORT: Usage and distribution of this source code is free. You use
it completely on your own risk.

Support
-------

Please send support requests to
[stefan@destructor.de](mailto:stefan@destructor.de). I'll do my best to
help you.

Postcardware
------------

If you like this code, please send a postcard of your city to my address
(anonymous if you want):

       Stefan Heymann
        Eschenweg 3
        72076 Tübingen
        GERMANY

* * * * *

2001-04-28, Stefan Heymann
