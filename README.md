# qbclient
A simple qBittorrent remote command-line client based on the bash, curl and jq binaries for a Linux-compatible environment (including Cygwin and Termux). It uses the last WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)

The file bin/qbcient is a bash-script which invokes a bash-subshell after an authorization to the server. Usage:

qbclient [(-k | -c CERTFILE)] [-l USERNAME] [-p PASSWORD] URL [BASH_OPT1] [BASH_OPT2] ...

The option -k implies an insecure https connection. The option -c is for pointing the self-signed cerificate of qBittorrent Web UI. The default value for USERNAME is "admin". Without the option "-p" the password will be asked from stdin. The expected value for URL is either "http://host[:port]", or "https://host[:port]".

In the invoked subshell a number of bash functions are defined which realize a client functionality (see the function description below). All these functions can be called through the qbclient options in the bash syntax, for example:

qbclient -p 'the_password' http://localhost:8080 -c "qbprefedit -s web_ui_max_auth_fail_count 10"

It is very convenient to use the qbclient installed on the qBittorrent server itself via an ssh- or mosh-access. Then to add a torrent file remotely one can use a pipeline to ssh-command (plus some extra quotation of arguments):

cat filename.torrent | ssh user@host qbclient -p "'adminadmin'" "'http://localhost:8888'" -c "'qblistadd -f -'"

Any bug reports, improvements, forks, alternative shell function systems are welcome.

### Dependencies and compatability

The code is tested on:

bash 5.0.18

curl 7.73.0

jq 1.6

qBittorrent 4.3.1

### Available functions:

#### 1. qbhelp

qbclient usage help

Usage: qbhelp [(list | [-u] FUNCTION)]

Usage: FUNCTION (-h|--help)

Options: 

list - list of available FUNCTIONs

-u FUNCTION - the usage of FUNCTION

FUNCTION - the help for FUNCTION

#### 2. qbversion

Print the versions of used software

Usage: qbversion [(app|api|build [qt|libtorrent|boost|openssl|bitness])]

#### 3. qbstatus

Show the current status of the server

Usage: qbstatus 

#### 4. qbpref

Show the value of specified preference property, or print all preferences

Usage: qbpref [(-r PREF_PROPERTY | PREF_PROPERTY_1 [PREF_PROPERTY_2 ...])]

With the "-r" option the function returns the raw value of a property

#### 5. qbprefedit

Send new values of specified preference properties

Usage: qbprefedit [-s] [-n] PREF_PROPERTY_1 NEW_VALUE_1 [PREF_PROPERTY_2 NEW_VALUE_2 ...]

The option -n prevents an additional check the values from the server after the request. The option "-s" forces "-n" and also prevents any output or user confirmations except (may be) error messages

To change the WebUI password by a secure way (from stdin) just omit the NEW_VALUE_1 argument in "qbprefedit web_ui_password". A change of web_ui_password property together with another options is not supported.

#### 6. qbaltspeed

Get the state of the alternative speed mode

Usage: qbaltspeed 

#### 7. qbaltspeededit

Set the state of the alternative speed mode

Usage: qbaltspeededit (true|false)

any nonepty argument except "true" and "false" causes a togglement of the mode

#### 8. qblist

List of torrents with a short info. The torrents are sorted by the addition time. The new torrents always in the end

Usage: qblist 

#### 9. qblistadd

Add a torrent by a URL (use quotes), or by a torrent FILE.

Usage: qblistadd ( -u 'URL'| -f FILE)  [ARG_1=VALUE_1] [ARG_2=VALUE_2] ...

If FILE='-' then the content of a torrent file will be read from stdin

The available ARGs are listed in WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)#add-new-torrent

#### 10. qblistremove

Remove the selected torrent.

Usage: qblistremove [-d] INDEX

The correct INDEX from qblist must be specifies. Option "-d" removes also the downloaded data Delete the selected torrent.

#### 11. qblistselect

Select the torrent with the specified index from qblist

Usage: qblistselect [-s] [INDEX]

alias qbtorselect=qblistselect

Option -s: silent mode

#### 12. qbtorselect

Select the torrent with the specified index from qblist.

Usage: qbtorselect [-s] [INDEX]

alias qbtorselect=qblistselect

Option -s: silent mode

#### 13. qbtordo

Pause, resume, recheck and reannounce of the selected torrent

Usage: qbtordo (pause | resume | recheck | reannounce)

#### 14. qbtorinfo

Get the specified information of the selected torrent, or print it completely

Usage: qbtorinfo [(-r INFO_PROPERTY | INFO_PROPERTY_1 [INFO_PROPERTY_2 ...])]

#### 15. qbtorinfolocation

Show the save location of the selected torrent

Usage: qbtorinfolocation 

#### 16. qbtorinfolocationedit

Set a new save location of the selected torrent

Usage: qbtorinfolocationedit NEW_LOCATION

#### 17. qbtorinfosequential

Show Sequential_Download and First_Last_Piece_Priority properties of the selected torrent

Usage: qbtorinfosequential 

#### 18. qbtorinfosequentialedit

Control of Sequential_Download and First_Last_Piece_Priority properties of the selected torrent

Usage: qbtorinfosequentialedit ARG1 [ARG2]

#### 19. qbtorblock

Scaled piece states string

Usage: qbtorblock [-n [NUMBER_OF_BLOCKS]] [(-o FILE_INDEX | -i FILE_INDEX1 [FILE_INDEX2])]

Print a fixed number of symbols showing the state of pieces blocks of the selected torrent.

Without the "-n" options the number of blocks equals to $COLUMNS (of the current terminal).

If the option "-n" is present then the default number equals to the number of pieces of the torrent (so block=piece).

The meaning of symbols:

"#" - completely downloaded block

"+" - partially downloaded block

"v" - downloading block

"-" - not downloaded (empty) block

The option "-o" specifies the index of a file, whose only pieces will be shown.

The option "-i" specifies the qbtorcontent interval of file indices whose pieces will be shown among other pieces. The pieces of files outside the interval will be marked by the dot (".")

#### 20. qbtorgeneral

Get the specified general properties of the selected torrent, or print all of them

Usage: qbtorgeneral [(-r GEN_PROPERTY | GEN_PROPERTY_1 [GEN_PROPERTY_2 ...])]

#### 21. qbtortrack

Trackers info of the selected torrent

Usage: qbtortrack 

#### 22. qbtortrackedit

Edit trackers of the selected torrent

Usage: qbtortrackedit (-d URL | URL_1 [URL_2])

Options: 

-d URL - Delete the tracker URL

URL_1 [URL_2] - Add the tracker URL_1, or replace URL_2 by URL_1

#### 23. qbtorpeer

List of connected peers of the selected torrent with a short info

Usage: qbtorpeer 

#### 24. qbtorpeeradd

Add a peer

Usage: qbtorpeeradd PEER

#### 25. qbtorcontent

List the files of the selected torrent with a short info

Usage: qbtorcontent [FILE_INDEX_1] [FILE_INDEX_2]

Without arguments list all files of the selected torrent in the alphabetical order with a short info.

If FILE_INDEX1 is indicated then the list starts with this index and finishes at FILE_INDEX2.

If no FILE_INDEX2 or FILE_INDEX2<FILE_INDEX1 the list contains only one item.

#### 26. qbtorfile

Get the info of the specified file of the selected torrent

Usage: qbtorfile FILE_INDEX [(-r PROPERTY | PROPERTY_1 [PROPERTY_2 ...])]

FILE_INDEX corresponds to the output of qbtorcontent

#### 27. qbtorfileprio

Show the file priorities of the selected torrent with a short info

Usage: qbtorfileprio [-r] [FILE_INDEX_1] [FILE_INDEX_2]

The arguments ARG_1 and ARG2 determine an interval of files similarly with qbtorcontent

With the "-r" option the function prints only priority values

#### 28. qbtorfileprioedit

Set priority of all files of the selected torrent, or set the priority for the files in the qbcontent() interval

Usage: qbtorfileprioedit NEW_PRIORITY [FILE_INDEX_1] [FILE_INDEX_2]

FILE_INDEX corresponds to the output of qbtorcontent

