---
layout: page
title: Photos
permalink: /photos/
---

Chase Jarvis said that the best camera is the one you have with you, but I would prefer shooting with my Nikon D750, if possible. On occasion, I post photos on Instagram under `@stratosphere._`

Here are some random images from my travels.

<p align="left">  <img width="600" src="../assets/gallery/albert-memorial.jpg"> </p>
*The Albert memorial near Imperial College*

<p align="left">  <img width="600" src="../assets/gallery/barcelona-sunsets.jpg"> </p>
*Sunset in Barcelona*

<p align="left">  <img width="600" src="../assets/gallery/dolomities-camper.jpg"> </p>
*Bittersweet memories of the Dolomites*

<p align="left">  <img width="600" src="../assets/gallery/first-manual.jpg"> </p>
*(re)learning control policies for manual driving in Denmark*

<p align="left">  <img width="600" src="../assets/gallery/foggy-castle.jpg"> </p>
*The Castle*

<p align="left">  <img width="600" src="../assets/gallery/foggy-fussen.jpg"> </p>
*Mysterious Fussen*

<p align="left">  <img width="600" src="../assets/gallery/snow-and-stars.jpg"> </p>
*Stars | Snow | Skiing*

<p align="left">  <img width="600" src="../assets/gallery/swiss-cycling.jpg"> </p>
*Cycling in Switzerland - uphills and downhills were equally fun*

<p align="left">  <img width="600" src="../assets/gallery/the-dawgs.jpg"> </p>
*Who let the dogs out?*


<!-- <div class="row">
  <div class="column">
    <img id="img0" src="https://profilepageimages.usecue.com/images/stratosphere._/0.jpg" alt="Pic1">
    <div class="left_capt">
        <i id="img0_caption"></i>
    </div>
  </div>
  <div class="column">
    <img id="img1" src="https://profilepageimages.usecue.com/images/stratosphere._/1.jpg" alt="Pic2">
    <div class="center_capt">
        <i id="img1_caption"></i>
    </div>
  </div>
  <div class="column">
    <img id="img2" src="https://profilepageimages.usecue.com/images/stratosphere._/2.jpg" alt="Pic3">
    <div class="right_capt">
        <i id="img2_caption"></i>
    </div>
  </div>
</div>

<div class="row">
  <div class="column">
    <img id="img3" src="https://profilepageimages.usecue.com/images/stratosphere._/3.jpg" alt="Pic4">
    <div class="left_capt">
        <i id="img3_caption"></i>
    </div>
  </div>
  <div class="column">
    <img id="img4" src="https://profilepageimages.usecue.com/images/stratosphere._/4.jpg" alt="Pic5">
    <div class="center_capt">
        <i id="img4_caption"></i>
    </div>
  </div>
  <div class="column">
    <img id="img5" src="https://profilepageimages.usecue.com/images/stratosphere._/5.jpg" alt="Pic6">
    <div class="right_capt">
        <i id="img5_caption"></i>
    </div>
  </div>
</div>

<div id="time"></div>

<script>
    function displayDate(targetElementId){
        // debug function
        var d = new Date();
        var n = d.toLocaleTimeString();
        document.getElementById(targetElementId).innerHTML = n;
    }

    function setImgFlex(imgId){
        // Sets the aspect ratio of the named object dynamnically.

        var attrs = document.getElementById(imgId).attributes;

        // get parent element (to set the flex ratio)
        var parent = document.getElementById(imgId).parentElement;

        // Debug to show attribute dimensions
        // var output = "";
        // for(var i = attrs.length - 1; i >= 0; i--) {
        //     output += attrs[i].name + "->" + attrs[i].value;
        // }
        // alert(output);

        var img = new Image();
        img.src = attrs.getNamedItem("src").value;
        // alert(img.src);
        var img_height, img_width, aspect_ratio;

        // this only works at runtime
        img.onload = function(){
            img_height = img.naturalHeight;
            img_width = img.naturalWidth;
            aspect_ratio = img_width/img_height;
            // alert(imgId + " " + img_width + " " + img_height + " " + aspect_ratio);
            parent.style.flexGrow = aspect_ratio.toString();
        }
    }

    function setCaptionCallback(data){
        for (var i = 0; i < data.length; i++) {
            if (i > 5) break;   // Only the first 6 photos need captions
            var caption = data[i]['caption'];
            caption = caption.split('#')[0];

            var elemId = "img" + i + "_caption";    // element name

            document.getElementById(elemId).innerHTML = caption;
        }
    }

    function getJsonForCaption(){
        // Callback to set captions from embedded json file
        fetch('https://profilepageimages.usecue.com/images/stratosphere._/images.json')
            .then(response => response.json())
            .then(data => setCaptionCallback(data));
    }

    // displayDate("time");
    setImgFlex("img0");
    setImgFlex("img1");
    setImgFlex("img2");
    setImgFlex("img3");
    setImgFlex("img4");
    setImgFlex("img5");
    getJsonForCaption();

</script> -->
