# qbclient
A simple qBittorrent remote command-line client based on the bash, curl and jq binaries for a Linux-compatible environment (including Cygwin). It uses the last WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)

The file bin/qbcient is a bash-script which calls a bash-subshell after an authorization to the server. Usage:

qbclient [-p PASSWORD] URL LOGIN [BASH_OPT1] [BASH_OPT2] ...

In the subshell a number of bash functions are defined which realize a client functionality (see the function description below). All these functions can be called through the qbclient options in the bash syntax, for example:

qbclient -p 'the_password' http://localhost:8080 admin -c "qbprefset -n web_ui_max_auth_fail_count 10"

Any bug reports, improvements, forks, alternative shell function systems are welcome.

### Dependencies and compatability

The code is tested on:

bash 5.0.18
curl 7.73.0
jq 1.6

### Available functions:

2. qblogout 
    
    Log out not leaving the subshell

1. qblogin [-p PASSWORD] URL LOGIN

    Log in to the qBittorent server (after logging out)

3. qbaddurl 'URL' (use quotes!)
    
    Add torrent by url
4. qbaddfile FILENAME
    
    Add torrent file
5. qbaddpaused FILENAME
    
    Add torrent file in the paused state
6. qbprefget [PREF_PROPERTY]
    
    Get the value of specified preference property, or print all preferences
7. qbprefset [-s] [-n] PREF_PROPERTY NEW_VALUE
    
    Set a new value of the preference property. The option -s is for a silent action without any output into stdout. The option -n forces -s and also prevents an additional check of the modified value from the server

8. qbpasswd

    Set WebUI password
    
9. qbinfo 
    
    List of torrents with a short info. The torrents are sorted by the addition time. The new torrents always are in the end 
10. qbselect [INDEX]
    
    Select the torrent with the specified index from qbinfo (). Get a short info for the selected torrent. 
11. qbdel INDEX
    
    Delete the selected torrent with the downloaded data. The correct index from qbinfo () must be specifies.
12. qbtorinfo [INFO_PROPERTY]
    
    Get the specified information of the selected torrent, or print it completely
13. qbtorprop [GEN_PROPERTY]
    
    Get the specified property of the selected torrent, or print all it's properties 
14. qbtrackers
    
    Trackers info of the selected torrent
15. qbcommand COMMAND  
    
    COMMAND=(pause | resume | recheck | reannounce)
    
    Pause, resume, recheck and reannounce of the selected torrent
16. qbmedia
    
    Toggle Sequential_Download and First_Last_Piece_Priority properties of the selected torrent
17. qbsetlocation NEW_LOCATION
    
    Set a new location of downloaded files of the selected torrent
18. qbpeers
    
    Peers info of the selected torrent
19. qbcontent
    
    List the files of the selected torrent with a short info
20. qbfileinfo FILE_INDEX
    
    FILE_INDEX corresponds to the output of qbcontent ()
    
    Get the info of the specified file of the selected torrent
21. qbfileprio NEW_PRIORITY [FILE_INDEX]
    
    Set priority of the specified file of the selected torrent, or set the priority for all it's files
    
#### The file lib/qbclient/qbfunctions

This file can be used without subshelling. Just use

source /path/to/qbfunctions

or 

. /path/to/qbfunctions

to have all these functions in the current shell.

### Experimental functions not declared in qBittorrent WebUI API

22. qb_get_torrent_file [FILE_NAME]
    
    Download the torrent file of the selected torrent to FILE_NAME
    
    See the code for a correct settings 
23. qb_add_url_paused 'URL' (use quotes!)
    
    Add url into the torrent client ensuring that the actual download will be paused
    
    just a collection of shell functions which may be loaded into the shell via  the "source" command:

