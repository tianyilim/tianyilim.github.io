---
layout: page
title: Photos
permalink: /photos/
---

Chase Jarvis said that the best camera is the one you have with you, but I would prefer shooting with my Nikon D750, if possible.

These are the 6 most recent photos from my Instagram account, `@stratosphere._`

<div class="row">
  <div class="column">
    <img id="img0" src="https://profilepageimages.usecue.com/images/stratosphere._/0.jpg" alt="Pic1">
  </div>
  <div class="column">
    <img id="img1" src="https://profilepageimages.usecue.com/images/stratosphere._/1.jpg" alt="Pic2">
  </div>
  <div class="column">
    <img id="img2" src="https://profilepageimages.usecue.com/images/stratosphere._/2.jpg" alt="Pic3">
  </div>
</div>

<div class="row">
  <div class="column">
    <img id="img3" src="https://profilepageimages.usecue.com/images/stratosphere._/3.jpg" alt="Pic4">
  </div>
  <div class="column">
    <img id="img4" src="https://profilepageimages.usecue.com/images/stratosphere._/4.jpg" alt="Pic5">
  </div>
  <div class="column">
    <img id="img5" src="https://profilepageimages.usecue.com/images/stratosphere._/5.jpg" alt="Pic6">
  </div>
</div>

<div id="time"></div>


<script>
    function displayDate(targetElementId){
        var d = new Date();
        var n = d.toLocaleTimeString();
        document.getElementById(targetElementId).innerHTML = n;
    }

    function setImgFlex(imgId){
        // Sets the aspect ratio of the named object dynamnically

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

    displayDate("time");
    setImgFlex("img0");
    setImgFlex("img1");
    setImgFlex("img2");
    setImgFlex("img3");
    setImgFlex("img4");
    setImgFlex("img5");
</script>