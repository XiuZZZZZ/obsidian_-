```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .display {
        display: none;
        position: absolute;
        background-color: white;
        border: 1px solid black;
        padding: 10px;
      }
    </style>
  </head>
  <body>
    <svg width="500" height="500">
      <rect x="50" y="50" width="100" height="100" onmouseover="showPopup(evt)" onmouseout="hidePopup()"/>
      <rect x="250" y="250" width="100" height="100" onmouseover="showPopup(evt)" onmouseout="hidePopup()"/>
    </svg>

    <div id="display" class="display">
      <p id="display-text">浮动框显示内容示例</p>
    </div>
  </body>
  <script>
    var display = document.getElementById("display");
    var displayText = document.getElementById("display-text");
  
    function showPopup(evt) {
      var target = evt.target;
      var svg = target.ownerSVGElement;
      var pt = svg.createSVGPoint();
  
      pt.x = evt.clientX;
      pt.y = evt.clientY;
  
      var mousePt = pt.matrixTransform(svg.getScreenCTM().inverse());
  
      displayText.innerHTML = "这是 " + target.tagName + " 元素";
      display.style.display = "block";
      display.style.top = (mousePt.y + 10) + "px";
      display.style.left = (mousePt.x + 10) + "px";
    }
  
    function hidePopup() {
      display.style.display = "none";
    }
  </script>
</html>
```