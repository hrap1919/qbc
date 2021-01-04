# qbclient
A simple qBittorrent remote command-line client based on the bash, curl and jq binaries for a Linux-compatible environment (including Cygwin and Termux). It uses the last WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)

The file bin/qbcient is a bash-script which invokes a bash-subshell after an authorization to the server. Usage:

qbclient [(-k | -c CERTFILE)] [-l USERNAME] [-p PASSWORD] URL [BASH_OPT1] [BASH_OPT2] ...

The option -k implies an insecure https connection. The option -c is for pointing the self-signed cerificate of qBittorrent Web UI. The default value for USERNAME is "admin". Without the option "-p" the password will be asked from stdin. The expected value for URL is either "http://host[:port]", or "https://host[:port]".

In the invoked subshell a number of bash functions are defined which realize a client functionality (see the function description below). All these functions can be called through the qbclient options in the bash syntax, for example:

qbclient -p 'the_password' http://localhost:8080 -c "qbprefset -s web_ui_max_auth_fail_count 10"

Any bug reports, improvements, forks, alternative shell function systems are welcome.

### Dependencies and compatability

The code is tested on:

bash 5.0.18

curl 7.73.0

jq 1.6

qBittorrent 4.3.1

### Available functions:

1. qbhelp

qbclient usage help
 
Usage: qbhelp [(list | [-u] FUNCTION)]
 
Options: 
list - list of available FUNCTIONs
-u FUNCTION - the usage of FUNCTION
FUNCTION - the help for FUNCTION

2. qbreauth

Re-authorize to the server with the same user/password
 
Usage: qbreauth 

3. qbtoradd

Add a torrent by a URL (use quotes), or by a torrent FILE.
 
Usage: qbtoradd ( -u 'URL'| -f FILE)  [API_PROP_NAME_1 API_PROP_VALUE_1 [API_PROP_NAME_2 API_PROP_VALUE_2 ...]]
 
Each OPTION has the form ARG=VALUE for ARGs listed in WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)#add-new-torrent

4. qbprefget

Get the value of specified prefernce property, or print all preferences
 
Usage: qbprefget [(-r PREF_PROPERTY | PREF_PROPERTY_1 [PREF_PROPERTY_2 ...])]
 
With the "-r" option the function returns the raw value of a property

5. qbprefset

Send new values of specified preference properties
 
Usage: qbprefset [-s] [-n] PREF_PROPERTY_1 NEW_VALUE_1 [PREF_PROPERTY_2 NEW_VALUE_2 ...]
 
The option -n prevents an additional check the values from the server after the request. The option "-s" forces "-n" and also prevents any output or user confirmations except (may be) error messages

6. qbpasswd

Change WebUI password
 
Usage: qbpasswd 

7. qbinfo

List of torrents with a short info. The torrents are sorted by the addition time. The new torrents always in the end
 
Usage: qbinfo 

8. qbselect

Select the torrent with the specified index from qbinfo ()
 
Usage: qbselect [-s] [INDEX]
 
Option -s: silent mode

9. qbdel

Delete the selected torrent. The correct index from qbinfo () must be specifies.
 
Usage: qbdel INDEX

10. qbtorinfo

Get the specified information of the selected torrent, or print it completely
 
Usage: qbtorinfo [(-r INFO_PROPERTY | INFO_PROPERTY_1 [INFO_PROPERTY_2 ...])]

11. qbtorprop

Get the specified property of the selected torrent, or print all it's properties
 
Usage: qbtorprop [(-r GEN_PROPERTY | GEN_PROPERTY_1 [GEN_PROPERTY_2 ...])]

12. qbtrackers

Trackers info of the selected torrent
 
Usage: qbtrackers 

13. qbtrackedit

Edit trackers of the selected torrent
 
Usage: qbtrackedit (-d URL | URL_1 [URL_2])
 
Options: 
-d URL - Delete the tracker URL
URL_1 [URL_2] - Add the tracker URL_1, or replace URL_2 by URL_1

14. qbcommand

Pause, resume, recheck and reannounce of the selected torrent
 
Usage: qbcommand (pause | resume | recheck | reannounce)

15. qbpeeradd

Add a peer
 
Usage: qbpeeradd PEER

16. qbmedia

Control of Sequential_Download and First_Last_Piece_Priority properties of the selected torrent
 
Usage: qbmedia ARG1 [ARG2]
 
Set Sequential_Download property of the selected torrent to ARG1. Set First_Last_Piece_Priority property either to ARG2, or to ARG1 (if ARG2 is absent). Each of ARG1 and ARG2 must be equal to either "true" or "false"

17. qblocationset

Set new location of downloaded files of the selected torrent
 
Usage: qblocationset NEW_LOCATION

18. qbpeers

Peers info of the selected torrent
 
Usage: qbpeers 

19. qbpieceview

Scaled piece states string
 
Usage: qbpieceview [-n [NUMBER_OF_BLOCKS]] [(-o FILE_INDEX | -i FILE_INDEX1 [FILE_INDEX2])]
 
Print a fixed number of symbols showing the state of pieces blocks of the selected torrent.
Without the "-n" options the number of blocks equals to $COLUMNS (of the current terminal).
If the option "-n" is present then the default number equals to the number of pieces of the torrent (so block=piece).
The meaning of symbols:
 
"#" - completely downloaded block
 
"+" - partially downloaded block
 
"v" - downloading block
 
"-" - not downloaded (empty) block
 
The option "-o" specifies the index of a file, whose only pieces will be shown.
The option "-i" specifies the interval of file indices whose pieces will be shown among other pieces. The pieces of files outside the interval will be marked by the dot (".")

20. qbcontent

List the files of the selected torrent with a short info
 
Usage: qbcontent [FILE_INDEX_1] [FILE_INDEX_2]
 
Without arguments list all files of the selected torrent in the alphabetical order with a short info.
If FILE_INDEX1 is indicated then the list starts with this index and finishes at FILE_INDEX2.
If no FILE_INDEX2 or FILE_INDEX2<FILE_INDEX1 the list contains only one item.

21. qbfileinfo

Get the info of the specified file of the selected torrent
 
Usage: qbfileinfo FILE_INDEX [(-r PROPERTY | PROPERTY_1 [PROPERTY_2 ...])]
 
FILE_INDEX corresponds to the output of qbcontent ()

22. qbfileprio

Set priority of all files of the selected torrent, or set the priority for the files in the qbcontent() interval
 
Usage: qbfileprio NEW_PRIORITY [FILE_INDEX_1] [FILE_INDEX_2]
 
FILE_INDEX corresponds to the output of qbcontent ()



#### The file lib/qbclient/qbfunctions

This file can be used without subshelling. Just use

source /path/to/qbfunctions

or 

. /path/to/qbfunctions

to have all these functions in the current shell. Use the function

qblogin [(-k | -c CERTFILE)] [-l USERNAME] [-p PASSWORD] URL

to authorize and "qblogout" to log out

