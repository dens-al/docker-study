Dockerfile for using `N_m3u8DL-RE`

https://github.com/nilaoda/N_m3u8DL-RE

Example to download from kinescope.io
```shell
docker run -v $(pwd):/downloads ghcr.io/dens-al/docker-study/video-downloader:main "https://kinescope.io/6An4dix8AmNxjBmhxGm3M1/master.mpd" --save-name 7.mp4 -sv best
```