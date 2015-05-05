# What is Sickrage?

Video File Manager for TV Shows, It watches for new episodes of your favorite shows and when they are posted it does its magic. It supports newsgroup and torrents with multiples built-in trackers.

> [More info](https://github.com/SiCKRAGETV/SickRage)

%%LOGO%%


# How to choose a tag

Sickrage support automatic updates in two ways :
-   Classic downloads
-   Git pull

The above tags provide two type of images :
- Master and Develop : which are based on the current respective branches and upgrade using git
- Version X.Y.Z : version based on tagged release which upgrade when a new release is out

# How to use this image.

Run the default Sickrage

	docker run sickrage:<yourtag>

You can test it by visiting `http://container-ip:8081` in a browser or, if you need access outside the host, on port 8081:

	docker run -p 8081:8081 sickrage:<yourtag>

You can then go to `http://localhost:8081` or `http://host-ip:8888` in a browser.

When sickrage update, the container is stopped, to get the latest update, you should run the container with --restart=always option

    docker run -p 8081:8081 --restart=always sickrage:<yourtag>

Then, when sickrage is updated and the container is stopped, docker will restart it.
