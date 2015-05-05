# WORK IN PROGRESS

# docker-sickrage

# What is Sickrage?

Video File Manager for TV Shows, It watches for new episodes of your favorite shows and when they are posted it does its magic. It supports newsgroup and torrents with multiples built-in trackers.

> [More info](https://github.com/SiCKRAGETV/SickRage)

![Sickrage](https://sickrage.tv/forums//filedata/fetch?filedataid=51)


# How to choose a tag

Sickrage support automatic updates in two ways :
-   Classic downloads
-   Git pull

The above tags provide two type of images :
- Master and Develop : which are based on the current respective branches and upgrade using git
- Version X.Y.Z : version based on tagged release which upgrade when a new release is out

# How to use this image.

Run the default Sickrage :

	docker run vsense/sickrage:<yourtag>

You can test it by visiting `http://container-ip:8081` in a browser or, if you need access outside the host, on port 8081 :

	docker run -p 8081:8081 vsense/sickrage:<yourtag>

You can then go to `http://localhost:8081` or `http://host-ip:8081` in a browser.

When sickrage update, the container is stopped, to get the latest update, you should run the container with --restart=always option :

    docker run -p 8081:8081 --restart=always vsense/sickrage:<yourtag>

Then, when sickrage is updated and the container is stopped, docker will restart it.

# Overriding

The image has two volumes :
-   /config : contains sickrage configuration
-   /downloads : contains the files downloads by the service provider of your choice : NZB, Torrents or Others. Also postprocessed files. You can pretty much drop whatever you want here it is sort of a data volume.

Sickrage is installed in the /sickrage directory but it is not a volume. If you wish to use host mount point instead of volumes it possible.

To use an on-host config (for persistent configuration if you do not want to deal with volumes, that I can understand :D) :

    docker run --restart=always --name sickrage --hostname sickrage -v /srv/configs/sickrage:/config vsense/sickrage

To mount your download folder (you will probably need to do that anyway) :

    docker run --restart=always --name sickrage --hostname sickrage vsense/sickrage -v /srv/seedbox:/downloads vsense/sickrage

You can even override sickrage directory if you prefer to git clone on you host for whatever reason :

    docker run --restart=always --name sickrage --hostname sickrage vsense/sickrage -v /srv/sickrage:/sickrage vsense/sickrage

And you can combin the commands above as you like :

    docker run --restart=always --name sickrage --hostname sickrage vsense/sickrage -v /srv/seedbox:/downloads -v /srv/sickrage:/sickrage -v /srv/configs/sickrage:/config vsense/sickrage

# Using systemd

Systemd :

    [Unit]
    Description=SickRage TV Downloader
    After=docker.service
    Requires=docker.service

    [Service]
    TimeoutStartSec=0
    ExecStartPre=-/usr/bin/docker stop sickrage
    ExecStartPre=-/usr/bin/docker kill sickrage
    ExecStartPre=-/usr/bin/docker rm -v -f sickrage
    ExecStartPre=-/usr/bin/docker pull vsense/sickrage:<yourtag>
    ExecStart=/usr/bin/docker run --restart=always --name sickrage --hostname sickrage vsense/sickrage
    ExecStop=/usr/bin/docker stop sickrage
    ExecStopPost=-/usr/bin/docker kill sickrage
    ExecStopPost=-/usr/bin/docker rm -v -f sickrage
    ExecReload=/usr/bin/docker restart sickrage
    RestartSec=5

    [Install]
    WantedBy=multi-user.target
