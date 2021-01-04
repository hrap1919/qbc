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


1. qbreauth

    Re-authorize to the server with the same credentials

2. qbtoradd (-u 'URL' | -f FILENAME) [OPTIONS]
    
    Add a torrent by a URL (use quotes), or by a torrent FILE. Each OPTION has the form ARG=VALUE for ARGs listed in WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)#add-new-torrent
3. qbprefget [(-r PREF_PROPERTY | PREF_PROPERTY_1 [PREF_PROPERTY_2 ...])]
    
    Print specified preference properties, or print all preferences. With the "-r" option the function returns the raw value of a property
4. qbprefset [-s] [-n] PREF_PROPERTY_1 NEW_VALUE_1 [PREF_PROPERTY_2 NEW_VALUE_2 ...]
    
    Send new values of specified  preference properties. The option -n prevents an additional check the values from the server after the request. The option "-s" forces "-n" and also prevents any output or user confirmations except (may be) error messages

5. qbpasswd

    Set WebUI password
    
6. qbinfo 
    
    List of torrents with a short info. The torrents are sorted by the addition time. The new torrents always are in the end 
7. qbselect [INDEX]
    
    Select the torrent with the specified index from qbinfo (). Get a short info for the selected torrent. 
8. qbdel INDEX
    
    Delete the selected torrent with the downloaded data. The correct index from qbinfo () must be specified.
9. qbtorinfo [INFO_PROPERTY]
    
    Get the specified information of the selected torrent, or print it completely
10. qbtorprop [GEN_PROPERTY]
    
    Get the specified property of the selected torrent, or print all it's properties 
11. qbtrackers
    
    Trackers info of the selected torrent
12. qbcommand COMMAND  
    
    COMMAND=(pause | resume | recheck | reannounce)
    
    Pause, resume, recheck and reannounce of the selected torrent

13. qpeeradd PEER

    Add a peer to the selected torrent. The PEER format is host:port

14. qbmedia ARG1 [ARG2]
    
    Set  Sequential_Download property of the selected torrent to ARG1. Set First_Last_Piece_Priority property either to ARG2, or to ARG1 (if ARG2 is absent). Each of ARG1 and ARG2 must be equal to either "true" or "false" Other values cause a tooglement of the properties.
15. qblocationset NEW_LOCATION
    
    Set a new location of downloaded files of the selected torrent
16. qbpeers
    
    Peers info of the selected torrent
17. qbcontent [FILE_INDEX1] [FILE_INDEX2]
    
    Without arguments list all files of the selected torrent in the alphabetical order with a short info. If FILE_INDEX1 exists then the list starts with this index and finishes at FILE_INDEX2. If FILE_INDEX2 does not exists or FILE_INDEX2<FILE_INDEX1 the list contains only one item.
18. qbfileinfo FILE_INDEX
    
    FILE_INDEX corresponds to the output of qbcontent ()
    
    Get the info of the specified file of the selected torrent
19. qbfileprio NEW_PRIORITY [FILE_INDEX1] [FILE_INDEX2]
    
    Set priority of all files of the selected torrent, or set the priority for the files in the qbcontent() interval
    
20. qbpieceview [-n [NUMBER_OF_BLOCKS]] [(-o FILE_INDEX | -i FILE_INDEX1 [FILE_INDEX2])]

    Print a fixed number of symbols showing the state of pieces blocks of the selected torrent. Without the "-n" options the number of blocks equals to $COLUMNS (of the current terminal). If the option "-n" is present then the default number equals to the number of pieces of the torrent (so block=piece). The meaning of symbols:
    
    "#" - completely downloaded block
    
    "+" - partially downloaded block
    
    "v" - downloading block
    
    "-" - not downloaded (empty) block
    
    The option "-o" specifies the index of a file, whose only pieces will be shown. The option "-i" specifies the interval of file indices whose pieces will be shown among other pieces. The pieces of files outside the interval will be marked by the dot (".").
#### The file lib/qbclient/qbfunctions

This file can be used without subshelling. Just use

source /path/to/qbfunctions

or 

. /path/to/qbfunctions

to have all these functions in the current shell. Use the function

qblogin [(-k | -c CERTFILE)] [-l USERNAME] [-p PASSWORD] URL

to authorize and "qblogout" to log out

### Experimental functions not declared in qBittorrent WebUI API

*. qb_get_torrent_file [FILE_NAME]
    
    Download the torrent file of the selected torrent to FILE_NAME
    
    See the code for a correct settings 
**. qb_add_url_paused 'URL' (use quotes!)
    
    Add url into the torrent client ensuring that the actual download will be paused
    
    just a collection of shell functions which may be loaded into the shell via  the "source" command:

