# APIs

> [!tip] JS Apis
>
> - media
> - Drag and drop
> - Geolocation
> - Browser Storage
> - Form
> - Canvas

---

### Media Api

> `<video src ="path and name for video file" width height muted loop autoplay controls poster></video>`
> all accessiable through video object (selected from dom)
>
> - video.play()
> - video.pause()
> - video.currentTime
> - video.duration
> - video.volume
> - video.muted
> - video.loop
> - video.autoplay
> - video.controls
> - video.poster
> - video.width
> - video.height

> mediaplayer.html (html & css)

```html
<!-- controls -->
<!-- control bar is browser dependent
we create our own bar 
-->
<!-- autoplay => have to be muted first to be autoplayed -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Video</title>
    <style>
      video {
        width: 100%;
      }
    </style>
  </head>
  <body>
    <details>
      <summary>Play | Pause</summary>
      <input type="button" value="Play" onclick="playpausebtn()" />
    </details>
    <details>
      <summary>Media Size</summary>
      <select name="size" id="size" onchange="changeSize()">
        <option value="small">Small</option>
        <option value="medium">Medium</option>
        <option value="large">Large</option>
      </select>
    </details>
    <video src="video.mp4" controls autoplay muted loop></video>
    <!-- rest of the html in mediaplayer.html -->
    <script></script>
  </body>
</html>
```

```js
window.addEventListener("load", function () {
  playpausebtn = this.document.getElementById("playpausebtn");
  video = this.document.querySelector("video");

  function playpausebtn() {
    if (video.paused) {
      video.play();
      playpausebtn.value = "Pause";
    } else {
      video.pause();
      playpausebtn.value = "Play";
    }
  }
  // change size
  size = this.document.getElementById("size");
  //use width property not style.width
  function changeSize() {
    switch (size.value) {
      case "small":
        video.width = 320;
        break;
      case "medium":
        video.width = 640;
        break;
      case "large":
        video.width = 1280;
        break;
      //fullscreen
      case "fullscreen":
        video.requestFullscreen();
        //wi
        break;
    }
  }
  //change volume
  // video.volume default => 1
  //value on html does not affect its volume
  // to do so we change it on load
  function changeVolume(e) {
    video.volume = e.target.value;
  }
  //mute -
function mutefun() {
  if (myvideo.muted) {
      myvideo.muted = false;
  } else {
      myvideo.muted = true;
  }
}
//change currentTime

function seekBar(){
  // we need seek bar to change with video
  // event fires everytime the video seekBar dealed with by User.

}
});
```

> [!tip] we show video object we use dot(.) on object
> `myvidoe.volume`

> [!danger] `video.currentTime` vs `video.currentSrc`

> [!info] `video.playbackRate`
> default => 1

```js
function forwardfun() {
  video.playbackRate++;
}
function backwardfun() {
  video.playbackRate--;
  if (video.playbackRate <= 0) {
    video.playbackRate = 1;
    //stop pause when backward
  }
}
```

> [!tip] `<audio src="" controls/>`

> [!example] cover for video
> `<video src="" poster=""/>` poster is the cover

> [!danger] how to display subtitles
> enhancements over lecture demos + labs

> [!danger] how to have fallback videos
>
> fallback based on different extension .mp4 etc

<!-- using  sources-->
<video>
<source src=""/>
<source src=""/>
<source src=""/>
</video>

<!-- different resolution -->

> [!bug] SEARCH

---

### Drag and Drop

> [!info] draggable vs undragable
> section is not draggable by default
> we can make it draggable by adding draggable attribute
> image is draggable by default

> [!tip] drag and drop events
>
> - dragstart
> - drag
> - dragend
> - dragenter
> - dragover
> - dragleave
> - drop

> [!bug] container is the one listening to drag events
> except for dragstart,dragend it is the element itself

> draganddropapi.html

```js
//on drag => copy of the element is created
//need to store the element
//dataTransfer : used to transfer data

window.addEventListener("load", function () {
  left = document.getElementById("left");
  right = document.getElementById("right");
  allimgs = document.querySelectorAll("img");
  for (let i = 0; i < allimgs.length; i++) {
    allimgs[i].addEventListener("dragstart", startdrag);
    allimgs[i].addEventListener("dragend", enddrag);
  }
  left.addEventListener("drop", drop);
  left.addEventListener("dragover", dragover);
  left.addEventListener("dragenter", dragenter);
  right.addEventListener("dragover", dragover);
  right.addEventListener("dragleave", dragleave);
});
function startdrag(e) {
  console.log(e.target.outerHTML);
  //store the element
  //dataTransfer
  e.dataTransfer.setData("img", e.target.outerHTML);
  // drop is not fired unless we prevent default
  ////////////////MUST/////////////////////
  ////////////////// DO NOT prevent default in dragstart as it will prevent drag
}
function drop(e) {
  ///////////browser prevent drop by default
  ///////////////////////prevent default in  all events to make it work
  e.preventDefault();
  left.innerHTML += e.dataTransfer.getData("img");
}
function dragover(e) {
  e.preventDefault();
}
function dragenter(e) {
  e.preventDefault();
  //change background color on dragenter
  left.style.backgroundColor = "red";
}
function enddrag(e) {
  e.preventDefault();
  //hide the element on dragend
  e.target.style.display = "none";
}
DragEvent; //{dataTransfer:DataTransfer, ...}
```

> [!danger] Enhancement
> from every product 2 pieces
> after second drop hide the element
> randomise stock so everyy product has different stock number
> we can use data attribute to store stock number in html`<img src="" data-stock="5"/>` `data-stock` is the attribute
>
> > [!success] we can access it using `e.target.dataset.stock`

> [!example] we need to implement return product
>
> we need only the drag to be to the left section so we need to prevent draggin outside the left section

---

# Break

---

### Browser Storage

> [!info] cookie vs local storage vs session storage
>
> - cookie => 4kb
> - local storage => 5mb - 10mb >> session Storage
> - session storage => 5mb
> - cookie => sent to server with every request

```js
//color picker
function changeColor(e) {
  console.log(e.target.value); //hexadecimal representation of the color
  //change color of the body
  document.body.style.backgroundColor = e.target.value;
  //store the color in local storage
  localStorage.setItem("color", e.target.value);
}
```

---

> [!tip] input HTML5 validation
> how to customize messege insted of the default
> email
> `<input type="email">`
> number
> `<input type="number" min="0" max="100" step="5">` check the number is between 0 and 100 and step is 5
> search
> `<input type="search">`
> date
> `<input type="date" min="2020-01-01" max="2020-12-31">`

> [!example] data list

```html
<input type="text" list="colors" />
<!-- list is the id of the datalist -->
<datalist id="colors">
  <option value="red"></option>
  <option value="green"></option>
  <option value="blue"></option>
</datalist>
<!-- placeholder -->
<input type="text" placeholder="Enter your name" />
<!-- required -->
<input type="text" required />
<!-- required to be filled -->

<!-- label accessibility -->
<label for="name">Name</label>
<input type="text" id="name" />
<!-- on click on label => focus on input -->

<!-- output readablity -->
<!-- sum 2 numbers -->
<input type="number" id="num1" />
<input type="number" id="num2" />
<output for="num1 num2" id="sum" />
```

---

### Geolocation

> [!example] geolocation
> strict permission API: we need to ask for permission
> collect location data about the user to provide location based services
> coords: latitude and longitude
> Timestamp: date and time in milliseconds

> [!success] Steps for geolocation api
>
> 1. check if the browser supports geolocation (true:continue/false:messege)
> 2. ask for permission (true:get info from user/false:messege)

```js
window.addEventListener("load", function () {
        function getloactionfun() {
            // 1- check exience
            if (navigator.geolocation) {
                // exist
                // 2- get permission  : using method getCurrentPosition(successCallback,errorcallback);
                navigator.geolocation.getCurrentPosition(getposition, errorhandler);
            } else {
                //not exist
                mymap.innerText = "Update Your Browser And Try Again !";
            }
        }
        function getposition(position) {
            lat = position.coords.latitude;
            lon = position.coords.longitude;

           /* google steps 
            * 1- create location object with lat lon values which getted from geolocation     using google constructor
            * 2- determine specs of map : center : created location obejcts  : zoom
            * 3- display map
            */
            var location = new google.maps.LatLng(lat, lon);
            //2- specify specs of map : zoom : , center
            var specs = { zoom: 17, center: location };
            // 3 retrive map and display map
            new google.maps.Map(mymap, specs);

        }
        function errorhandler() {
            alert("Error");
        }
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(success, fail);
  } else {
    alert("Browser does not support geolocation");
  }
});
```

> [!tip] we can use google maps api to display the location on map
>
> > [!tip] how to get ur own google maps api
> > search
> > use the one in lecture

> [!example] google maps api
> 1.create object from google maps latlon (lat,lon)=>location
> 2.specs for the map:{zoom: 17,center: location}

```js
function getPosition(e) {
  console.log(e); //GeolocationPosition{coords:GeolocationCoordinates,timestamp:}
  console.log(e.coords); //GeolocationCoordinates{latitude: ,longitude: ,accuracy: ,altitude: ,altitudeAccuracy: ,heading: ,speed: }
  console.log(e.coords.latitude); //latitude
  console.log(e.timestamp); //timestamp in milliseconds changes everytime we call the function partially different
  console.log(new Date(e.timestamp)); //date and time
  var lat = e.coords.latitude;
  var long = e.coords.longitude;
  myMap.innerHTML = `Latitude: ${lat} <br> Longitude: ${long}`;
  alert("success");

  //dealing with google maps api
  var location = new google.maps.LatLng(lat, long);
  var specs = {
    zoom: 17,
    center: location,
  };
  var map = new google.maps.Map(myMap, specs);
}
```

> [!danger] Enhancement will have deadline as bonus

# #lab

> - video list
> - drag and drop
> - map with marker
> - addition - validation (NAN 🚫)
> - canvas demo - (task:optional)
