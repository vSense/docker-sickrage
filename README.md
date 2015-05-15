# docker-sickrage

# What is Sickrage?

Video File Manager for TV Shows, It watches for new episodes of your favorite shows and when they are posted it does its magic. It supports newsgroup and torrents with multiples built-in trackers.

> [More info](https://github.com/SiCKRAGETV/SickRage)

![Sickrage](https://raw.githubusercontent.com/vSense/docker-sickrage/master/logo.png)


# How to choose a tag

Sickrage support automatic updates in two ways :
-   Classic downloads
-   Git pull

The above tags provide several type of images :
- Master and Develop : which are based on the current respective branches and upgrade using git
- Version X.Y.Z : version based on tagged release which upgrade when a new release is out
- With or without an init process : sickrage or supervisord as PID 1

Depending on how you are planning to launch sickrage you have to choose the right image

# How to use this image.

Run Sickrage :

	docker run vsense/sickrage:<yourtag>

You can test it by visiting `http://container-ip:8081` in a browser or, if you need access outside the host, on port 8081 :

	docker run -p 8081:8081 vsense/sickrage:<yourtag>

You can then go to `http://localhost:8081` or `http://host-ip:8081` in a browser.

## Thought about auto update and init systems

Sickrage is updated a lot and it is important to be up-to-date with the latest version. It kind of goes against the principle of build repeatability with docker where you have a "static" software version in your container.

Update management with Docker is kind of a PITA and there are differents approach.

### Without init system in the container

If you use the sickrage tag then sickrage runs as PID 1, when sickrage updates it will shutdown itself and try to relaunch but as there is not process in the container, the container stops. The container is stopped, to get the latest update, you should run the container with --restart=always option :

    docker run -p 8081:8081 --restart=always vsense/sickrage:<yourtag>

Then, when sickrage is updated and the container is stopped, docker will restart it.

This method works fine but if you are using an init system on the host (like systemd) then when the container stops, it is the docker daemon which will relaunch the container and systemd will lose track of the process. It is not that bad but it kind of defeat the purpose of systemd and you will loose some feature like logging to journald. It will work though with the systemd param *RemainAfterExit=yes*

    [Unit]
    Description=SickRage TV Downloader
    After=docker.service
    Requires=docker.service

    [Service]
    TimeoutStartSec=0
    RemainAfterExit=yes
    ExecStartPre=-/usr/bin/docker stop sickrage
    ExecStartPre=-/usr/bin/docker kill sickrage
    ExecStartPre=-/usr/bin/docker rm -v -f sickrage
    ExecStartPre=-/usr/bin/docker pull sickrage:4.0.19
    ExecStart=/usr/bin/docker run --restart=always --name sickrage --hostname sickrage sickrage:4.0.19
    ExecStop=/usr/bin/docker stop sickrage
    ExecStopPost=-/usr/bin/docker kill sickrage
    ExecStopPost=-/usr/bin/docker rm -v -f sickrage
    ExecReload=/usr/bin/docker restart sickrage
    RestartSec=5

    [Install]
    WantedBy=multi-user.target

### With an init process in the container

If you are using the supervisor tag then supervisord runs as PID 1, when sickrage updates it will shuutdown itself and try to relaunch. Since it is not the only process in the container, the container will keep running. From this point there are three options :
-   Supervisord without auto-restart : sickrage shutdown, relaunch itself, supervisord loose track of the process. Not ideal
-   Supervisord with auto-restart : sickrage shutdowns, relaunch itself, supervisord relaunch sickrage too. You have two sickrage process running in parallels
-   Supervisord with auto-restart and shutdown only in sickrage : the latest version has an option to disable the automatic relaunch of sickrage and only shutdown (*no_restart = 1* in config.ini)

With the supervisord it is possible to use a systemd, get sickrage up-to-date and still keeping the features of systemd, just be careful to use the no-restart option in sickrage to avoid conflicts.

    [Unit]
    Description=SickRage TV Downloader
    After=docker.service
    Requires=docker.service

    [Service]
    TimeoutStartSec=0
    ExecStartPre=-/usr/bin/docker stop sickrage
    ExecStartPre=-/usr/bin/docker kill sickrage
    ExecStartPre=-/usr/bin/docker rm -v -f sickrage
    ExecStartPre=-/usr/bin/docker pull sickrage:4.0.19
    ExecStart=/usr/bin/docker run --name sickrage --hostname sickrage sickrage:4.0.19
    ExecStop=/usr/bin/docker stop sickrage
    ExecStopPost=-/usr/bin/docker kill sickrage
    ExecStopPost=-/usr/bin/docker rm -v -f sickrage
    ExecReload=/usr/bin/docker restart sickrage
    RestartSec=5

    [Install]
    WantedBy=multi-user.target

With this, systemd will launch the container, supervisord will launch sickrage, sickrage stderr/stdout will be pipe to container stdout/stdin wich wil be pipe to journald. So you can use :
-   Docker logs
-   Journalctl
-   Supervisorctl
-   Systemd status

And have a coherent view of the unit.

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
