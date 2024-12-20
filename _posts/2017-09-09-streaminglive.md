---
layout: post
title: How to embed a ScreenCast into a Web Page through Node.js
tags: dyi, nodejs, ffmpeg, streaming
date: 2017-09-09
categories: sample-posts
tabs: true
description: This brief post will tell ya how to create a live stream and to make it available to any user that has a web browser. This is possible since you can always redirect a mpeg stream into a WebSocket, and then show the content in a web page via JavaScript thanks to WebGL and Canvas2D renderers.
---

This brief post will tell ya how to create a live stream and to make it available to any user that has a web browser. This is possible since you can always redirect a mpeg stream into a WebSocket, and then show the content in a web page via JavaScript thanks to WebGL and Canvas2D renderers. 

First, you must have ffmpeg installed in your local computer, where the stream is going to originate. Then, on a remote computer that will provide the streaming, you must [install node.js](https://github.com/nodesource/distributions#debinstall) and then clone the [jsmpeg repository](https://github.com/phoboslab/jsmpeg).
At this step, you must edit line 86 from `websocket-relay.js` as follows:

     }).listen(STREAM_PORT, 'my.remote.ip.address');

By doing this, you force the proxy to listen from a remote connection instead of doing it on localhost. Moreover, you have to `npm install` the modules `ws` and `http-server`.
After doing so, you can start the http server and the proxy as follows:

     node websocket-relay.js <yourpw> 8081 8082 & 
     http-server

At this point, you can start streaming from your local computer towards your streaming proxy as follows:

     ffmpeg     -f x11grab -s 1280x720 -framerate 30 -i :0.0 \
                -c:v libx264 -preset veryfast -tune zerolatency \
                -pix_fmt yuv444p -f mpegts -codec:v mpeg1video \
                -s 800x654 -b:v 1000k -bf 0 \
                http://my.remote.ip.address:8081/<yourpw>

Finally, your streaming will be available via browser as follows:

     http://my.remote.ip.address:8080/view-stream.html
