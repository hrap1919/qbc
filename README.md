# qbclient
A qBittorrent shell remote client based on curl and jq binaries. It uses the last WebUI API https://github.com/qbittorrent/qBittorrent/wiki/WebUI-API-(qBittorrent-4.1)

The file qbcient is just a collection of shell functions which may be loaded into the shell via  the "source" command:

source qbclient

or 

. qbclient

## Available functions:

1. qblogin URL LOGIN PASSWORD

    Login to the qBittorent server
2. qblogout 
    
    Log out
3. qbaddurl "URL" (use quotes!)
    
    Add url
4. qbaddfile FILENAME
    
    Add torrent file
5. qbaddaused FILENAME
    
    Add torrent file in the paused state
6. qbprefget [PREF_PROPERTY]
    
    Get the value of specified prefernce property, or print all preferences
7. qbprefset PREF_PROPERTY NEW_VALUE
    
    Set a new value of the preference property
8. qbinfo 
    
    List of torrents with a short info. The torrents are sorted by the addition time. The new torrents always in the end Usage: qbinfo
9. qbselect INDEX
    
    Select the torrent with the specified index from qbinfo (). P
10. qbdel INDEX
    
    Delete the selected torrent with the downloaded data. The correct index from qbinfo () must be specifies.
11. qbtorinfo [INFO_PROPERTY]
    
    Get the specified information of the selected torrent, or print it completely
12. qbpropinfo [GEN_PROPERTY]
    
    Get the specified property of the selected torrent, or print all it's properties 
13. qbtrackers
    
    Trackers info of the selected torrent
14. qbcommand COMMAND  
    
    COMMAND=(pause | resume | recheck | reannounce)
    
    Pause, resume, recheck and reannounce of the selected torrent
15. qbmedia
    
    Toggle Sequential_Download and First_Last_Piece_Priority properties of the selected torrent
16. qbsetlocation NEW_LOCATION
    
    Set new location of downloaded files of the selected torrent
17. qbsetpeers
    
    Peers info of the selected torrent
18. qbcontent
    
    List the files of the selected torrent with a short info
19. qbfileinfo FILE_INDEX
    
    FILE_INDEX corresponds to the output of qbcontent ()
    
    Get the info of the specified file of the selected torrent
20. qbfileprio NEW_PRIORITY [FILE_INDEX]
    
    Set priority of the specified file of the selected torrent, or set the priority for all it's files
    
# Experimental functions not declared in qBittorrent WebUI API

21. qb_get_torrent_file [FILE_NAME]
    
    Download the torrent file of the selected torrent to FILE_NAME
    
    See the code for a correct settings 
22. qb_add_url_paused "URL" (use quotes!)
    
    Add url into the torrent client ensuring that the actual download will be paused
