# What is Sickrage?

Video File Manager for TV Shows, It watches for new episodes of your favorite shows and when they are posted it does its magic. It supports newsgroup and torrents with multiples built-in trackers.

[More info](https://github.com/SickRage/SickRage)

![Sickrage](https://raw.githubusercontent.com/vSense/docker-sickrage/master/logo.png)

# How to choose a tag

Available tags:
-   `latest` : based on master branch

[![](https://images.microbadger.com/badges/version/vsense/sickrage.svg)](http://microbadger.com/images/vsense/sickrage "Get your own version badge on microbadger.com") [![](https://images.microbadger.com/badges/image/vsense/sickrage.svg)](http://microbadger.com/images/vsense/sickrage "Get your own image badge on microbadger.com")

-   `develop` : based on develop branch

[![](https://images.microbadger.com/badges/version/vsense/sickrage:develop.svg)](http://microbadger.com/images/vsense/sickrage:develop "Get your own version badge on microbadger.com") [![](https://images.microbadger.com/badges/image/vsense/sickrage:develop.svg)](http://microbadger.com/images/vsense/sickrage:develop "Get your own image badge on microbadger.com")

# How to use this image.

Run Sickrage :

```
docker run vsense/sickrage:<yourtag>
```

You can test it by visiting `http://container-ip:8081` in a browser or, if you need access outside the host, on port 8081 :

```
docker run -p 8081:8081 vsense/sickrage:<yourtag>
```

You can then go to `http://localhost:8081` or `http://host-ip:8081` in a browser.

# Overriding

The image has two volumes :
-   /config : contains sickrage configuration
-   /downloads : contains the files downloads by the service provider of your choice : NZB, Torrents or Others. Also postprocessed files. You can pretty much drop whatever you want here it is sort of a data volume.

Sickrage is installed in the /sickrage directory but it is not a volume. If you wish to use host mount point instead of volumes it possible.

To use an on-host config (for persistent configuration if you do not want to deal with volumes, that I can understand :D) :

    docker run --restart=always --name sickrage --hostname sickrage -v /srv/configs/sickrage:/config vsense/sickrage

To mount your download folder (you will probably need to do that anyway) :

    docker run --restart=always --name sickrage --hostname sickrage  -v /srv/configs/sickrage:/config -v /srv/seedbox:/downloads vsense/sickrage

You can even override sickrage directory if you prefer to git clone on you host for whatever reason :

    docker run --restart=always --name sickrage --hostname sickrage -v /srv/sickrage:/sickrage vsense/sickrage

And you can combin the commands above as you like :

    docker run --restart=always --name sickrage --hostname sickrage -v /srv/seedbox:/downloads -v /srv/sickrage:/sickrage -v /srv/configs/sickrage:/config vsense/sickrage
