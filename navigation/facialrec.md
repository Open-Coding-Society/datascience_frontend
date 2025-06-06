---
layout: post
title: Facial Recognition
search_exclude: true
permalink: /facial/
author: Tanisha Patil
---

<div id="facial-wrapper">
  <div class="video-container">
    <video id="video" width="620" height="480" autoplay muted class="video"></video>
  </div>
</div>

<style>
  #facial-wrapper {
    background: linear-gradient(#FFBF46, #FF99C9);
    margin: 0;
    padding: 0;
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }

  canvas {
    position: absolute;
  }

  .video {
    border: 10px solid white;
    border-radius: 8vh;
  }

  .video-container {
    display: flex;
  }
</style>

<script>
  const modelPath = "{{ site.baseurl }}/models";
</script>

<script defer src="{{ site.baseurl }}/facial/face-api.min.js"></script>
<script defer src="{{ site.baseurl }}/facial/cscript.js"></script>
