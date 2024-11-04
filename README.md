[![Deploy](https://github.com/bermarques/http-flv-docker/workflows/Deploy/badge.svg)](https://github.com/bermarques/http-flv-docker/actions?query=workflow%3ADeploy)

## Supported tags and respective `Dockerfile` links

- [`latest` _(Dockerfile)_](https://github.com/bermarques/nginx-http-flv-docker/blob/master/Dockerfile)

**Note**: There are [tags for each build date](https://hub.docker.com/r/bermarques/nginx-http-flv/tags). If you need to "pin" the Docker image version you use, you can select one of those tags. E.g. `bermarques/nginx-rtmp:latest-2020-08-16`.

# nginx-http-flv

[**Docker**](https://www.docker.com/) image with [**Nginx**](http://nginx.org/en/) using the [**nginx-http-flv-module**](https://github.com/winshining/nginx-http-flv-module) module for live multimedia (video) streaming.

## Description

This [**Docker**](https://www.docker.com/) image can be used to create an RTMP server for multimedia / video streaming using [**Nginx**](http://nginx.org/en/) and [**nginx-http-flv-module**](https://github.com/winshining/nginx-http-flv-module), built from the current latest sources (Nginx 1.23.2 and nginx-http-flv-module 1.2.11).

This was inspired by other similar previous images from [tiangolo](https://hub.docker.com/r/tiangolo/nginx-rtmp)

The main purpose (and test case) to build it was to allow streaming from [**OBS Studio**](https://obsproject.com/) to different clients at the same time.

**GitHub repo**: <https://github.com/bermarques/http-flv-docker>

**Docker Hub image**: <https://hub.docker.com/r/bermarques/http-flv/>

## Details

## How to use

- For the simplest case, just run a container with this image:

```bash
docker run -d -p 1935:1935 --name nginx-http-flv bermarques/http-flv
```

## How to test with OBS Studio and VLC

- Run a container with the command above

- Open [OBS Studio](https://obsproject.com/)
- Click the "Settings" button
- Go to the "Stream" section
- In "Stream Type" select "Custom Streaming Server"
- In the "URL" enter the `rtmp://<ip_of_host>/live` replacing `<ip_of_host>` with the IP of the host in which the container is running. For example: `rtmp://192.168.0.30/live`
- In the "Stream key" use a "key" that will be used later in the client URL to display that specific stream. For example: `test`
- Click the "OK" button
- In the section "Sources" click the "Add" button (`+`) and select a source (for example "Screen Capture") and configure it as you need
- Click the "Start Streaming" button

- Open a [VLC](http://www.videolan.org/vlc/index.html) player (it also works in Raspberry Pi using `omxplayer`)
- Click in the "Media" menu
- Click in "Open Network Stream"
- Enter the URL from above as `rtmp://<ip_of_host>/live/<key>` replacing `<ip_of_host>` with the IP of the host in which the container is running and `<key>` with the key you created in OBS Studio. For example: `rtmp://192.168.0.30/live/test`
- Click "Play"
- Now VLC should start playing whatever you are transmitting from OBS Studio

## Debugging

If something is not working you can check the logs of the container with:

```bash
docker logs nginx-http-flv
```

## Extending

If you need to modify the configurations you can create a file `nginx.conf` and replace the one in this image using a `Dockerfile` that is based on the image, for example:

```Dockerfile
FROM bermarques/http-flv

COPY nginx.conf /etc/nginx/nginx.conf
```

The current `nginx.conf` contains:

```Nginx
worker_processes auto;
rtmp_auto_push on;
events {}
rtmp {
    server {
        listen 1935;
        listen [::]:1935 ipv6only=on;

        application live {
            live on;
            record off;
        }
    }
}
```

You can start from it and modify it as you need. Here's the [documentation related to `nginx-http-flv-module`](https://github.com/winshining/nginx-http-flv-module/wiki/Directives).

## Technical details

- This image is built from the same base official images that most of the other official images, as Python, Node, Postgres, Nginx itself, etc. Specifically, [buildpack-deps](https://hub.docker.com/_/buildpack-deps/) which is in turn based on [debian](https://hub.docker.com/_/debian/). So, if you have any other image locally you probably have the base image layers already downloaded.

- It is built from the official sources of **Nginx** and **nginx-http-flv-module** without adding anything else.

- It has a simple default configuration that should allow you to send one or more streams to it and have several clients receiving multiple copies of those streams simultaneously. (It includes `rtmp_auto_push` and an automatic number of worker processes).

## License

This project is licensed under the terms of the MIT License.
