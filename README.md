# Pīwakawaka IIIF Image Navigator

A controlled experience of an IIIF image in an OpenSeadragon (OSD) viewer.

The Pīwakawaka library adds a wrapper around an
[OpenSeadragon](https://openseadragon.github.io/) viewer object
(designed to present zoomable [IIIF images](http://iiif.io/)).
It takes over control of OpenSeadragon, presenting selected points of interest
(using pan, zoom, pauses, and labels) in a configurable sequence.

## Demo

Pīwakawaka is used in [Arotahi](https://staplegun.github.io/arotahi/)

## Usage

The library is simple Javascript so include it directly.

NB: Currently it does not work in Internet Explorer (as the code uses `class`
and there is no transpiling pipeline set up).

```HTML
<canvas id="canvas" />
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/openseadragon/2.3.1/openseadragon.min.js"></script>
<script src="https://cdn.rawgit.com/richtr/NoSleep.js/v0.7.1/dist/NoSleep.js"></script>
<script src="https://rawgit.com/staplegun/piwakawaka/master/piwakawaka.min.js"></script>

<script type="text/javascript">
let iiifUrl = "https://media.tepapa.govt.nz/collection/58998/iiif"

let piwakawakaNavigator = new Piwakawaka(null, viewerStarted, viewerStopped)
// NB: web browsers only allow fullscreen if the user initiates it
piwakawakaNavigator.setFullscreen(false)
piwakawakaNavigator.setImageLocation(iiifUrl)
piwakawakaNavigator.play()

function viewerStarted() {
    console.log("viewer started")
}
function viewerStopped() {
    console.log("viewer stopped")
}
</script>
```

## Config

* `image` - URL for IIIF image
* `speed` - Roughly 10 pixels per second, e.g. speed 5 = pan at 50px/sec
* `fillViewer` - If `true`, zooms in slightly so there are no letterbox black bars
* `crop` - Sets a cropped rectangle area within the image so auto-pan operations stay within that cropped box, as: `[x,y,width,height]` (see 'Implementation notes' below)
* `sequence` - Array of objects defining the points to pan to

Sequence commands
* `min` - minimum image pixel on this dimension (0 or cropped min)
* `max` - maximum image pixel on this dimension (image or cropped max)
* `same` - current image pixel location (i.e. no change)
* `next` - location that scrolls forward by the size that is currently visible
* `prev` - location that scrolls backwards by the size that is currently visible

```javascript
let sequence = [
  {
    "x": "min",
    "y": "min",
    "width": 800,
    "height": 600,
    "duration": 7,
    "delay": 2,
    "note": "Zoom to top left corner"
  },
  {
    "x": "max",
    "y": "same",
    "note": "Pan right"
  }
]
let piwakawakaConfig = {
  "image": "https://media.tepapa.govt.nz/collection/58998/iiif",
  "speed": 20,
  "fillViewer": true,
  "crop": [0,0,2000,2000],
  "sequence": sequence,
}
let piwakawaka = new Piwakawaka(piwakawakaConfig)
// or
let piwakawaka = new Piwakawaka()
piwakawaka.init(piwakawakaConfig)
```

## Implementation notes

OpenSeadragon uses three image co-ordinate systems:
1. image = pixels of the source image (as at original (100%) resolution), e.g. width: 0-4000, height: 0-5000
2. viewport = percentage of the source image, e.g. width: 0 - 1, height: 0 - 1
3. web container = pixels of the HTML element showing a part of the source image, e.g. width: 0 - 300, height: 0 - 400

Most calculations here use source image co-ordinates (1 above), which are
converted to/from viewport or web co-ordinates as necessary for OSD operations.
While this does increase the number of calculations/conversions, it makes it
easier to define locations to show, e.g. it is easier to specify a region of
an image when looking at the original image in image editor software.

Regions are specified as rectangles, following OSD's
[Rect](https://openseadragon.github.io/docs/OpenSeadragon.Rect.html) structure
(excluding rotation) as `{x,y,w,h}`:
- `x` = horizontal position of the top-left corner of the region rectangle, where 0 is the left edge of the source image
- `y` = vertical position of the top-left corner of the region rectangle, where 0 is the top edge of the source image
- `w` = horizontal width of the rectangle (to the right)
- `h` = vertical height of the rectangle (downwards)

For example, `{20,40,300,500}` defines a rectangle that starts 20 pixels from
the left edge of the source image and 40 pixels from its top,
then extends 300 pixels to the right and 500 pixels downwards.

## Documentation

* [Pīwakawaka API documentation](https://staplegun.github.io/piwakawaka/)
* [Pīwakawaka GitHub repository](https://github.com/staplegun/piwakawaka/)

## Requires

* [OpenSeadragon](https://openseadragon.github.io/)
* [NoSleep.js](https://github.com/richtr/NoSleep.js)
* [jQuery](https://jquery.com/)

## Pīwakawaka name

Named after the Pīwakawaka (_pee-wahkah-wahkah_) bird
([New Zealand Fantail](https://en.wikipedia.org/wiki/New_Zealand_fantail))
which typically flits about constantly from place to place looking for tasty morsels.

## Licence

This project is licensed under the MIT Licence - see the
[LICENSE](LICENSE) file for details
