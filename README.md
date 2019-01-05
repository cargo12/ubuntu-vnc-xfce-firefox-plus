# Headless Ubuntu/Xfce containers with VNC/noVNC and configurable Firefox

## accetto/ubuntu-vnc-xfce-firefox-plus

[Docker Hub][this-docker] - [Git Hub][this-github] - [Changelog][this-changelog] - [Wiki][this-wiki]

**Attention:** Resources for building images with default Firefox installation without configuration support can be found in its own GitHub repository [ubuntu-vnc-xfce-firefox][accetto-github-ubuntu-vnc-xfce-firefox]. Resources for building base images are in the GitHub repository [accetto/ubuntu-vnc-xfce][accetto-github-ubuntu-vnc-xfce].

***

**This repository** contains resources for building Docker images based on [Ubuntu][docker-ubuntu], with [Xfce][xfce] desktops, headless **VNC**/[noVNC][novnc] environments and the current [Firefox][firefox] web browser with pre-configuration support.

This image can be successfully built and used on Linux, NAS devices and Windows.

It has been tested with [Docker CE][docker-ce] on [Ubuntu flavours][ubuntu-flavours], with [Container Station][container-station] on a NAS from [QNAP][qnap] and with [Docker for Windows][docker-for-windows] on Windows 10.

The image is perfect for fast creation of light-weight web browser containers. They can be thrown away easily and replaced quickly, improving browsing privacy. They run under a non-root user by default, improving browsing security.

They make also excellent long-term browsers, because the preferences and profiles can be pre-configured and stored on volumes that survive container destruction.

There are two ways of customization. Firstly, it's possible to force Firefox preferences by modifying the provided **user.js** file. Secondly, it's possible to use a complete Firefox profile, previously created on a volume. The [HOWTO][this-wiki-howto] page in [Wiki][this-wiki] describes it in more details.

Frequently used preferences and profiles can be also embedded into new user built images. The ready-to-use Dockerfiles are already provided (see [below](#user-content-image-set)). The [HOWTO][this-wiki-howto] page in [Wiki][this-wiki] describes how to build such images.

The image is based on the [accetto/ubuntu-vnc-xfce][accetto-docker-ubuntu-vnc-xfce] image, just adding the [Firefox][firefox] browser and resources for its customization.

The image inherits the following components from its [base image][accetto-docker-ubuntu-vnc-xfce]:

- light-weight [Xfce][xfce] desktop environment
- high-performance VNC server [TigerVNC][tigervnc] (TCP port **5901**)
- [noVNC][novnc] HTML5 clients (full and lite) (TCP port **6901**)
- popular text editor [vim][vim]
- lite but advanced graphical editor [mousepad][mousepad]
- container start-up options

Running containers in background is the primary scenario this image has been developed for. However, running in foreground can be useful in many cases. See the description below for examples of using the containers both ways.

The image is regularly maintained and rebuilt. The history of notable changes is documented in [CHANGELOG][this-changelog].

![screenshot-container][screenshot-container]

### Image set

- [accetto/ubuntu-vnc-xfce-firefox-plus][this-docker]

  - `latest` based on `accetto/ubuntu-vnc-xfce:latest`
  - `rolling` based on `accetto/ubuntu-vnc-xfce:rolling`

    [![version badge](https://images.microbadger.com/badges/version/accetto/ubuntu-vnc-xfce-firefox-plus.svg)](https://microbadger.com/images/accetto/ubuntu-vnc-xfce-firefox-plus "Get your own version badge on microbadger.com") [![size badge](https://images.microbadger.com/badges/image/accetto/ubuntu-vnc-xfce-firefox-plus.svg)](https://microbadger.com/images/accetto/ubuntu-vnc-xfce-firefox-plus "Get your own image badge on microbadger.com") [![version badge](https://images.microbadger.com/badges/version/accetto/ubuntu-vnc-xfce-firefox-plus:rolling.svg)](https://microbadger.com/images/accetto/ubuntu-vnc-xfce-firefox-plus:rolling "Get your own version badge on microbadger.com") [![size badge](https://images.microbadger.com/badges/image/accetto/ubuntu-vnc-xfce-firefox-plus:rolling.svg)](https://microbadger.com/images/accetto/ubuntu-vnc-xfce-firefox-plus:rolling "Get your own image badge on microbadger.com")

- **accetto/ubuntu-vnc-xfce-firefox-plus-preferences**

    These images are not actually contained in the [Docker repository][accetto-docker], because they are intended to keep pre-configured user-specific **Firefox preferences**. Users can put their favorite preferences into the configuration files and build the images using the provided [Dockerfile-plus-preferences][this-dockerfile-plus-preferences]. The [HOWTO][this-wiki-howto] page in [Wiki][this-wiki] describes it in more details.

- **accetto/ubuntu-vnc-xfce-firefox-plus-profile**

    These images are also not actually contained in the [Docker repository][accetto-docker], because they are intended to keep pre-configured user-specific **Firefox profiles**. Users can prepare full Firefox profiles and build the images using the provided [Dockerfile-plus-profile][this-dockerfile-plus-profile]. The [HOWTO][this-wiki-howto] page in [Wiki][this-wiki] describes it in more details.

### Ports

The images expose the following **TCP** ports:

- **5901** used for access over **VNC**
- **6901** used for access over [noVNC][novnc]

The default **VNC user** password is **headless**.

### Volumes

The images do not create or use any external volumes by default. However, the following folders make good mounting points:

- /home/headless/Documents/
- /home/headless/Downloads/
- /home/headless/Music/
- /home/headless/Pictures/
- /home/headless/Public/
- /home/headless/Templates/
- /home/headless/Videos/

The following mounting point is specific to Firefox:

- /home/headless/.mozilla

Both *named volumes* and *bind mounts* can be used. More about volumes can be found in [Docker documentation][docker-doc] (e.g. [Manage data in Docker][docker-doc-managing-data]).

## Running containers in background (detached)

Created containers will run under the non-root user **headless:headless** by default.

The following container will listen on automatically selected **TCP** ports of the host computer:

```docker
docker run -d -P accetto/ubuntu-vnc-xfce-firefox-plus
```

The following container will listen on the host's **TCP** ports **25901** (VNC) and **26901** (noVNC):

```docker
docker run -d -p 25901:5901 -p 26901:6901 accetto/ubuntu-vnc-xfce-firefox-plus
```

The following container wil create or re-use the local named volume **my\_Downloads** mounted as `/home/headless/Downloads`. The container will be accessible through the same **TCP** ports as the one above:

```docker
docker run -d -P -v my_Downloads:/home/headless/Downloads accetto/ubuntu-vnc-xfce-firefox-plus
```

or using the newer syntax with **--mount** flag:

```docker
docker run -d -P --mount source=my_Downloads,target=/home/headless/Downloads accetto/ubuntu-vnc-xfce-firefox-plus
```

More usage examples can be found in [Wiki][this-wiki] (section [HOWTO][this-wiki-howto]).

## Running containers in foreground (interactively)

The image supports the following container start-up options: `--wait` (default), `--skip`, `--debug` (also `--tail-log`) and `--help`. This functionality is inherited from the [base image][accetto-docker-ubuntu-vnc-xfce].

The following container will print out the help and then it'll remove itself:

```docker
docker run --rm accetto/ubuntu-vnc-xfce-firefox-plus --help
```

Excerpt from the output, which describes the other options:

```docker
OPTIONS:
-w, --wait      (default) Keeps the UI and the vnc server up until SIGINT or SIGTERM are received.
                An optional command can be executed after the vnc starts up.
                example: docker run -d -P accetto/ubuntu-vnc-xfce
                example: docker run -it -P accetto/ubuntu-vnc-xfce /bin/bash

-s, --skip      Skips the vnc startup and just executes the provided command.
                example: docker run -it -P accetto/ubuntu-vnc-xfce --skip /bin/bash

-d, --debug     Executes the vnc startup and tails the vnc/noVNC logs.
                Any parameters after '--debug' are ignored. CTRL-C stops the container.
                example: docker run -it -P accetto/ubuntu-vnc-xfce --debug

-t, --tail-log  same as '--debug'

-h, --help      Prints out this help.
                example: docker run --rm accetto/ubuntu-vnc-xfce
```

It should be noticed, that the `--debug` start-up option does not show the command prompt even if the `-it` run arguments are provided. This is because the container is watching the incoming vnc/noVNC connections and prints out their logs in real time. However, it is easy to attach to the running container like in the following example.

In the first terminal window on the host computer, create a new container named **foo**:

```docker
docker run --name foo accetto/ubuntu-vnc-xfce-firefox-plus --debug
```

In the second terminal window on the host computer, execute the shell inside the **foo** container:

```docker
docker exec -it foo /bin/bash
```

## Using headless containers

There are two ways, how to use the created headless containers. Note that the default **VNC user** password is **headless**.

### Over VNC

To be able to use the containers over **VNC**, a **VNC Viewer** is needed (e.g. [TigerVNC][tigervnc] or [TightVNC][tightvnc]).

The VNC Viewer should connect to the host running the container, pointing to the host's TCP port mapped to the container's TCP port **5901**.

For example, if the container has been created on the host called `mynas` using the parameters described above, the VNC Viewer should connect to `mynas:25901`.

### Over noVNC

To be able to use the containers over [noVNC][novnc], an HTML5 capable web browser is needed. It actually means, that any current web browser can be used.

The browser should navigate to the host running the container, pointing to the host's TCP port mapped to the container's TCP port **6901**.

However, the containers offer two [noVNC][novnc] clients - **lite** and **full**. The connection URL differs slightly in both cases. To make it easier, a simple startup page is implemented.

If the container have been created on the host called `mynas` using the parameters described above, then the web browser should navigate to `http://mynas:26901`.

The startup page will show two hyperlinks pointing to the both noVNC clients:

- `http://mynas:26901/vnc_lite.html`
- `http://mynas:26901/vnc.html`

It's also possible to provide the password through the links:

- `http://mynas:26901/vnc_lite.html?password=headless`
- `http://mynas:26901/vnc.html?password=headless`

## Issues

If you have found a problem or just have a question, please check the [Issues][this-issues] and the [Troubleshooting][this-wiki-troubleshooting], [FAQ][this-wiki-faq] and [HOWTO][this-wiki-howto] pages in [Wiki][this-wiki] first. Please do not overlook also the closed issues.

If you do not find a solution, you can file a new issue. The better you describe the problem, the bigger the chance it'll be solved soon.

[this-docker]: https://hub.docker.com/r/accetto/ubuntu-vnc-xfce-firefox-plus/
[this-github]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus

[this-changelog]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/blob/master/CHANGELOG.md
[this-issues]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/issues

[this-wiki]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/wiki
[this-wiki-howto]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/wiki/How-to
[this-wiki-troubleshooting]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/wiki/Troubleshooting
[this-wiki-faq]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/wiki/Frequently-asked-questions

[this-dockerfile-plus-preferences]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/blob/master/Dockerfile-plus-preferences
[this-dockerfile-plus-profile]: https://github.com/accetto/ubuntu-vnc-xfce-firefox-plus/blob/master/Dockerfile-plus-profile

[accetto-github]: https://github.com/accetto/
[accetto-docker]: https://hub.docker.com/u/accetto/

[accetto-github-ubuntu-vnc-xfce]: https://github.com/accetto/ubuntu-vnc-xfce
[accetto-docker-ubuntu-vnc-xfce]: https://hub.docker.com/r/accetto/ubuntu-vnc-xfce/

[accetto-github-ubuntu-vnc-xfce-firefox]: https://github.com/accetto/ubuntu-vnc-xfce-firefox/

[docker-ubuntu]: https://hub.docker.com/_/ubuntu/
[docker-doc]: https://docs.docker.com/
[docker-doc-managing-data]: https://docs.docker.com/storage/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-ce]: https://docs.docker.com/install/

[qnap]: https://www.qnap.com/en/
[container-station]: https://www.qnap.com/solution/container_station/en/

[ubuntu-flavours]: https://www.ubuntu.com/download/flavours

[firefox]: https://www.mozilla.org
[mousepad]: https://github.com/codebrainz/mousepad
[novnc]: https://github.com/kanaka/noVNC
[tigervnc]: http://tigervnc.org
[tightvnc]: http://www.tightvnc.com
[vim]: https://www.vim.org/
[xfce]: http://www.xfce.org

[screenshot-container]: https://raw.githubusercontent.com/accetto/ubuntu-vnc-xfce-firefox-plus/master/ubuntu-vnc-xfce-firefox-plus.jpg
