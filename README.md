# dragging-from-scratch

LICENSE: MIT (c) 2015 Michael Romanovsky.

````
 body {
 position : absolute;
 margin   : 0;
}

.drag_container {
 position         : absolute;
 background-color : DarkGreen;
 width            : 1000px;
 height           : 1000px;
 left             : 100px;
 top              : 100px;
 display          : block;
}

.drag_item {
 position         : absolute;
 background-color : green;
 width            : 100px;
 height           : 100px;
 display          : block;
}
````

````
<div class="drag_container">
 <div class="drag_item" style="left:50px; top:50px"></div>
 <div class="drag_item" style="left:250px; top:50px"></div>
</div>
````

````
initialize_dragdrop ()

 function getXY (evt, target) {
  if (typeof target == "undefined") target = evt.target
  var rect = target.getBoundingClientRect(); return [evt.clientX - rect.left, evt.clientY - rect.top]
 }

function initialize_dragdrop () {
 function getRightClick (evt) {
  if (evt.which) return (evt.which == 3)
  if (evt.button) return (evt.button == 2)
 }
 
 // Set this page to selectable or otherwise.
 function disable_selection (obj) {
  var disableselect = function () {return false}
  if (typeof obj.onselectstart != "undefined") obj.onselectstart = disableselect
  obj.onmousedown   = disableselect
  obj.ondragstart   = disableselect
 }
 // </helper functions>
 
 var drag_object   = null
 var drag_offset_x = 0
 var drag_offset_y = 0
 
 function drag_move (evt) {
  if (typeof evt == "undefined") evt = window.event
  if (drag_object == null) return
  var xy = getXY (evt, drag_container)
  drag_object.style.left = (xy[0] - parseInt(window.getComputedStyle(drag_object)["width"]) / 2) + 'px'
  drag_object.style.top  = (xy[1] - parseInt(window.getComputedStyle(drag_object)["height"]) / 2) + 'px'
 }
 function drag_start (evt) {
  if (drag_object != null) return
  if (evt.target == evt.currentTarget) return
  drag_object = evt.target
  drag_object.style.pointerEvents = "none"
  drag_object.style.zIndex        = 1000
 }
 
 function drag_end (evt) {
  if (evt.currentTarget != evt.target) return
  if (drag_object == null) return
  drag_clear ()
 }
 
 function drag_clear () {
  drag_object.style.pointerEvents = ""
  drag_object.style.zIndex        = 0
  drag_object = null
 }
 
 // This one is messed up. Can't figure out how to set it up right.
 function drag_end_from_bounds (evt) {
  if (evt.currentTarget != evt.target) return
  if (drag_object == null) return
  var mouseX = evt.pageX; var mouseY = evt.pageY
  var windowScrollX = window.scrollX; if (typeof windowScrollX == "undefined") windowScrollX = document.body.scrollLeft
  var windowScrollY = window.scrollY; if (typeof windowScrollY == "undefined") windowScrollY = document.body.scrollTop
  if (((mouseY >= 0)) && ((mouseY <= window.innerHeight + windowScrollY)) && ((mouseX >= 0) && mouseX <= (window.innerWidth + windowScrollX))) return
 drag_clear ()
}
 
 var drag_container = document.querySelectorAll('.drag_container')[0]
 disable_selection (drag_container)
 
 drag_container.addEventListener ('mousemove', drag_move)
 drag_container.addEventListener ('mouseout' , drag_end_from_bounds)
 drag_container.addEventListener ('mouseup'  , drag_end)
 drag_container.addEventListener ('mousedown', drag_start)
}
````
