---
layout: post
title: Facial Recognition
search_exclude: true
permalink: /facial/
author: Tanisha Patil
---

<div class="video-container">
    <video id="video" width="620" height="480" autoplay muted class="video"></video>
</div>

<script>
    const modelPath = "{{ site.baseurl }}/models";
</script>

<script defer src="{{ site.baseurl }}/facial/face-api.min.js"></script>
<script defer src="{{ site.baseurl }}/facial/cscript.js"></script>

<style>
    body {
        background: linear-gradient(#FFBF46, #FF99C9);
        margin: 0;
        padding: 0;
        width: 100vw;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    canvas {
        position: absolute;
    }

    #captureEmotionButton {
        background-color: #1eb6af;
        color: white;
        padding: 4px 10px;
        font-size: 22px;
        border: none;
        cursor: pointer;
        border-radius: 5px;
        position: absolute;
        top: 1ch;
        font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    }

    #captureEmotionButton:hover {
        background-color: #61d9d3;
    }

    .emotionResult {
        position: absolute;
        bottom: 1ch;
        font-size: 30px;
        color: white;
        font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    }

    .video {
        border: 10px solid white;
        border-radius: 8vh;
    }

    .video-container {
        display: flex;
    }

    .button-container {
        display: flex;
        position: absolute;
        top: 7ch;
    }

    .func-button {
        cursor: pointer;
        background-color: #1eb6af;
        color: white;
        padding: 7px 10px;
        font-size: 18px;
        border: none;
        cursor: pointer;
        border-radius: 5px;
        margin-right: 15px;
    }

    .func-button:hover {
        background: #61d9d3;
    }
</style>
