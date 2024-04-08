# Partie 2
## 1. Premier script youtube-dl
### 📁 Le script /srv/yt/yt.sh

    #!/bin/bash
    #kaysx7
    yoououou



    if [[ $# -eq 0 ]]; then
        echo "Aucun lien youtube trouvé !"
        exit 1
    fi


    DOWNLOAD_FOLDER_NAME="downloads"
    LOG_FOLDER_PATH="/var/log/yt"
    LOG_DOWNLOAD_FILE_NAME="download.log"
    LOG_DESCRIPTION_FILE_NAME="description"

    localDirectory=$(dirname $(realpath "$0"))
    downloadDirectory="${localDirectory}/${DOWNLOAD_FOLDER_NAME}"


    videoName=$(youtube-dl --skip-download --get-title --no-warnings $1)
    videoDescription=$(youtube-dl --skip-download --get-description --no-warnings $1)

    futureVideoDirectory="${downloadDirectory}/${videoName}"
    mkdir -p "${futureVideoDirectory}"
    echo $videoDescription > "${futureVideoDirectory}/${LOG_DESCRIPTION_FILE_NAME}"

    youtube-dl -f mp4 -o "${futureVideoDirectory}/%(title)s-%(id)s.%(ext)s" --no-warnings $1 > /dev/null


    echo "La vidéo ${1} a été téléchargée."
    echo "Chemin du fichier : ${futureVideoDirectory}"


    if [ ! -d "${LOG_FOLDER_PATH}" ]; then
        exit
    fi

    currentDate=$(date +"%y/%m/%d %H:%M:%S")
    echo "[${currentDate}] La vidéo ${1} a été téléchargée. Chemin du fichier : ${futureVideoDirectory}" >> "${LOG_FOLDER_PATH}/${LOG_DOWNLOAD_FILE_NAME}"
#
### 📁 Le fichier de log
    [kayss@localhost ~]$ cat download.log
    [ 14/02/19 17:27:34 ] Video https://www.youtube.com/watch?v=2qimOu2W7V4 was downloaded. File path : /srv/yt/downloads/Boot Camp Day 44: Try not to Trade
#
### 🌞 Vous fournirez dans le compte-rendu, en plus du fichier, un exemple d'exécution avec une sortie

    [kayss@localhost ~]$ ./yt.sh https://www.youtube.com/watch?v=2qimOu2W7V4
    Video https://www.youtube.com/watch?v=2qimOu2W7V4 was downloaded.
    File path : /srv/yt/downloads/Boot Camp Day 44: Try not to Trade

        

        