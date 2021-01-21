# qbc - qBittorrent Bash Client
A simple qBittorrent remote command-line client based on the bash, curl and jq binaries for a Linux-compatible environment (including Cygwin and Termux). It uses the last WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)

The file bin/qbcient is a bash-script which invokes a bash-subshell after an authorization to the server. Usage:

```console
qbc [(-k | -c CERTFILE)] [-l USERNAME] [-p PASSWORD] URL [BASH_OPT1] [BASH_OPT2] ...
```

The option -k implies an insecure https connection. The option -c is for pointing the self-signed cerificate of qBittorrent Web UI. The default value for USERNAME is "admin". If PASSWORD parameter is set to "-" then the password will be asked from stdin. The expected value for URL is either "http://host[:port]", or "https://host[:port]". Alternatively, there is a possibility to use presets specific for each connection defined in ~/.qbcrc file (see .qbcrc_template). In the last case, 

In the invoked subshell a number of bash functions are defined which realize a client functionality (see the function description below). All these functions can be called through the qbc options in the bash syntax, for example:

```console
qbc -p 'adminadmin' http://localhost:8080 -c "qbprefedit -s web_ui_max_auth_fail_count 10"
```

It is very convenient to use ssh- or mosh-access with the qbc installed on the qBittorrent server itself. To add a torrent file remotely one can use a stdin redirection of ssh-command:

```console
ssh user@host qbc -p "'adminadmin'" "'http://localhost:8080'" -c "'qbadd -f -'" <filename.torrent
```

(or) cat console filename.torrent | ssh user@host qbc -p "'adminadmin'" "'http://localhost:8080'" -c "'qbadd -f -'" 

Any bug reports, improvements, forks, alternative shell function systems are welcome.

### Dependencies and compatability

The code is tested on:

bash 5.0.18

curl 7.73.0

jq 1.6

qBittorrent 4.3.1

### Available functions:


All functions have the prefix qb-. If the name of a function is started by qbtor- then it works only if a specific torrent is selected (i.e., the variable QBhash is set to the hash of the torrent). To select a torrent one can use the function qbselect.

#### 1. qbhelp

qbc usage help

Usage: qbhelp [(list | [-u] FUNCTION)]

Usage: FUNCTION (-h|--help)

Options: 

list - list of available FUNCTIONs

-u FUNCTION - the usage of FUNCTION

FUNCTION - the help for FUNCTION

#### 2. qbversion

Print the versions of used software

Usage: qbversion [(app|api|build [qt|libtorrent|boost|openssl|bitness])]

#### 3. qbnetwork

Show the general network status of the server

Usage: qbnetwork 

#### 4. qbpref

Show the value of specified preference property, or print all preferences

Usage: qbpref [(-r PREF_PROPERTY | PREF_PROPERTY_1 [PREF_PROPERTY_2 ...])]

With the "-r" option the function returns the raw value of a property

#### 5. qbprefedit

Send new values of specified preference properties

Usage: qbprefedit [-s] [-n] PREF_PROPERTY_1 NEW_VALUE_1 [PREF_PROPERTY_2 NEW_VALUE_2 ...]

The option -n prevents an additional check the values from the server after the request. The option "-s" forces "-n" and also prevents any output or user confirmations except (may be) error messages

To change the WebUI password by a secure way (from stdin) just omit the NEW_VALUE_1 argument in "qbprefedit web_ui_password". A change of web_ui_password property together with another options is not supported.

#### 6. qbmenu

Show editable options not included into the preferences:alternative speed mode, existing categories and tags

Usage: qbmenu [-r] [altspeed|categories|tags]

Option "-r" for a raw output of requested values

#### 7. qbmenuedit

Change alternative speed mode, categories and tags

Usage: qbmenuedit altspeed (true|false|toggle)

Usage: qbmenuedit categories -d NAME #(remove a category)

Usage: qbmenuedit categories NAME PATH #(create or edit a category)

Usage: qbmenuedit tags [-d] NAME #(create or remove a tag)

#### 8. qbadd

Add a torrent by a URL (use quotes), or by a torrent FILE.

Usage: qbadd ( -u 'URL'| -f FILE)  [ARG_1=VALUE_1] [ARG_2=VALUE_2] ...

If FILE='-' then the content of a torrent file will be read from stdin

The available ARGs are listed in WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)#add-new-torrent

#### 9. qblist

List of torrents with a short info.

Usage: qblist [--jselect FILTER] [--jsort FILTER] [--jformat FORMAT] [INDEX_1] [INDEX_2]

Without options print info of all torrents. The torrents are sorted by the addition time and indexed by numbers

Arguments INDEX_1 and INDEX_2 determine an interval of torrents to be shown

Advanced options: --jselect and --jsort for inserting "select" and "sort_by" jq-filters

E.g., qblist --jselect '(.category=="My Category")' --jsort '(.name)|reverse'

Advanced option: --jformat enables a possibility to define an own jq-format for output lines

E.g., the following command prints hashes and names of torrents which have the tag "My tag":

qblist --jselect '(.tags|split(", ")|.[]|select(.=="My tag"))' --jformat '.hash+"/"+.name'

The default format of qblist is:

```console
'(.index|tostring)+". "+.name+", "+(.progress*100|trunc|tostring)+"% of "+(.size /1048576|trunc|tostring)+"Mb, state:"+.state'
```

#### 10. qbdo

Pause, resume, recheck, reannounce and force start the torrents

Usage: qbdo (pause | resume | recheck | reannounce | setForceStart) [-i [INDEX_1] [INDEX_2]]

With the option "-i" the function does the action for the torrents within the qblist index interval, "-i" without INDEX_1 and INDEX_2 does the global action

without "-i" the function does the action for the selected torrent (via qbselect function)

#### 11. qbselect

Select the torrent with the specified index from qblist

Usage: qbselect [-s] [INDEX]

Option -s: silent mode

#### 12. qbtordel

Remove the selected torrent.

Usage: qbtordel [-s] [-d]

Option "-s" is for a silent removing without an interactive confirmation. # qbtordel: Option "-d" is for removing also the downloaded data

#### 13. qbtorinfo

Get the specified information of the selected torrent, or print it completely

Usage: qbtorinfo [(-r INFO_PROPERTY | INFO_PROPERTY_1 [INFO_PROPERTY_2 ...])]

#### 14. qbtormenu

Get editable properties of the selected torrent, or print all editable properties

Usage: qbtormenu [(-r VAR | VAR_1 [VAR_2 ...])]

The option "-r" is for the raw output of a property

#### 15. qbtormenuedit

Change an editable property of the selected torrent

Usage: qbtormenuedit PROPERTY VALUE # if the PROPERTY is a single-valued

Usage: qbtormenuedit PROPERTY VALUE_1 VALUE_2 ... # if the PROPERTY is a multi-valued object (not work for priority)

Usage: qbtormenuedit priority (top|increase|decrease|bottom)

Usage: qbtormenuedit PROPERTY [-d] ITEM # add or delete("-d") the ITEM from the array PROPERTY (tags)

#### 16. qbtorblock

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

The option "-i" specifies the qbtorcontent interval of file indices (alphabetical and generic) whose pieces will be shown among other pieces. The pieces of files outside the interval will be marked by the dot (".")

See "qbtorcontent --help" for a more info on generic and alphabetical indices

#### 17. qbtorgeneral

Get the specified general properties of the selected torrent, or print all of them

Usage: qbtorgeneral [(-r GEN_PROPERTY | GEN_PROPERTY_1 [GEN_PROPERTY_2 ...])]

#### 18. qbtortrack

Trackers info of the selected torrent

Usage: qbtortrack 

#### 19. qbtortrackedit

Edit trackers of the selected torrent

Usage: qbtortrackedit (-d URL | URL_1 [URL_2])

Options: 

-d URL - Delete the tracker URL

URL_1 [URL_2] - Add the tracker URL_1, or replace URL_2 by URL_1

#### 20. qbtorpeer

List of connected peers of the selected torrent with a short info

Usage: qbtorpeer 

#### 21. qbtorpeeradd

Add a peer

Usage: qbtorpeeradd PEER

#### 22. qbtorcontent

Alphabetically sorted list of the names and priorities of files of the selected torrent with a short info

Usage: qbtorcontent [--jselect FILTER] [--jsort FILTER] [--jformat FORMAT] [INDEX_1] [INDEX_2]

Without option print info of all files with alphabetical (0,1,2,...) and generic ([0],[1],[2]...)

The generic indices do not depend on renaming of files, they are shown in square brackets.

The alphabetical indices depend on the names, they may be more convenient than generic ones.

E.g., the record "3[1]. /Folder/file2 ..."  means that the generic index (GEN_INDEX) of file2 is [1], while it's alphabetical index (ALPH_INDEX) is 3.

Arguments INDEX_1 and INDEX_2 are either alphabetical (without brackets), or generic (in square brackets) indices of the same type. They determine an interval of files to be shown

Advanced options: --jselect and --jsort for inserting "select" and "sort_by" jq-filters

E.g., qbtorcontent --jselect '(.priority==7)' --jsort '(.progress)|reverse'

Advanced option: --jformat enables a possibility to define an own jq-format for output lines

E.g., qbtorcontent --jsort '(.gen_index)' --jformat '"["+(.gen_index|tostring)+"] "+.name'

The default format of qbtorcontent is:

```console
'(.alph_index|tostring)+"["+(.gen_index|tostring)+"]. "+.name+" <prio:"+(.priority|tostring)+">, "+(.progress*100|trunc|tostring)+"% of "+(.size /1048576|trunc|tostring)+"Mb, "+(.availability\*100|trunc|tostring)+"% online"'
```

#### 23. qbtorcontentedit

Change the name of a file and the priorities of files of the selected torrent

Usage: qbtorcontentedit name NEW_NAME INDEX

If the INDEX is alphatical (no brackets) then the function prints the generic index of the renamed file

If the INDEX is generic then function prints nothing to stdout

Usage: qbtorcontentedit prio NEW_PRIORITY [INDEX_1] [INDEX_2]

Without arguments  INDEX_1 and INDEX_2 the function does a global change of file priorities

With arguments INDEX_1 INDEX_2 the function sets a new priority to the files in the specified interval, the same as in qbcontent

See "qbcontent --help" for a more info on generic and alphabetical indices

#### 24. qbtorfile

Get the full info of the specified file of the selected torrent

Usage: qbtorfile INDEX [(-r PROPERTY | PROPERTY_1 [PROPERTY_2 ...])]

INDEX can be either alphabetic or generic

For example, "qbtorfile [n] -r alph_index" gives the alphabetic index of the generic index [n], while "qbtorfile n -r gen_index" does the reverse transformation

Try "qbtorcontent --help" for a more info on generic and alphabetical indices

