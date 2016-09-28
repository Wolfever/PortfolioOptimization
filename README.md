## Welcome to review my code! 

Here is a list of explanations for what I have done to optimize the index.html and main.js.

### Part 1: How I optimized the index.html 

#### Step 1: Compress all the pictures

When I test the original webpage on [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/?hl=zh-CN), it warns me that the pictures are too big. So I used grunt to minify the pictures, and used an [online picture compressing tool](http://compressjpeg.com/zh/) to further compress it. 

#### Step 2: Inline all the CSS and Js files 

PageSpeed Insights also told me to inline all the CSS and JS files. So I: 

* Inlined css/print.css and css/style.css
* Copied the web fonts style and inlined it
* Inlined js/perfmatters.js 

After these two steps, the page reached a score of over 90% in PageSpeed Insights. 

### Part 2: How I optimized main.js 
#### 1: Create just enough pizzas on the background
The final code looks like the following 

  ```bash
  var itemsNum = Math.floor((window.innerHeight + 100) / 256) * 8;
  for (var i = 0; i < itemsNum; i++) {
    var elem = document.createElement('img');
    ......
    document.querySelector("#movingPizzas1").appendChild(elem);
  }
  ```
#### 2: Optimize function updatePositions()

The final code looks like the following: 
  ```bash
  var scrollTopValue = document.body.scrollTop;
  for (var i = 0; i < itemsNum; i++) {
    var transformValue = 100 * Math.sin((scrollTopValue / 1250) + (i % 5)) - 100 * Math.sin(i % 5);
    var toTransform = 'translate(' + transformValue + 'px)';
    items[i].style.transform = toTransform ;
  }
  ```

1. I created a variable to store the current scroll top value, so the javascript doesn't have to stop each time and read the layout and then change it.  
1. I changed style.left to style.transform. Using transform means no more painting, which means less time. 
1. I recalculated how each img.mover needs to translate on the X axis by substracting the initial left value using the current left value. 
1. I created a layer for each img.mover so that when it moves, the whole page doesn't have to be repainted. 

  ```bash
  .mover {
    will-change: transform;
    transform: translateZ(0);
  }
  ```
1. To make the transform attribute to work, I added an initial value to each img.mover when they are created: 

  ```bash
    var leftValue  = 100 * Math.sin(i % 5) + (i % cols) * s;
    elem.style.left = leftValue + 'px';
  ```

#### 3: Optimize function changePizzaSizes(size) 

  ```bash
    function sizeSwitcher (size) {
      switch(size) {
        case "1":
          return 25;
        case "2":
          return 33.33333;
        case "3":
          return 50;
        default:
          console.log("bug in sizeSwitcher");
      }
    }

    var newWidth = sizeSwitcher(size);
    
    for(var i = 0; i < randomPizzaContainer.length; i++){
      randomPizzaContainer[i].style.width = newWidth + '%';
    }
  ```

##### About the insanity of the former code: 
The former code indicates that when the size is changed to a new size, computer should first read the initial width of the first element, then calculate its percentage in comparison to its parent container. After this, computer should calculate the desired width and then make out the difference between these two widths. Then oddly enough, computer has to add this difference to the former width of the first element, and change the style. Then computer repeats itself and figure out the width of the second element's width. 

##### The improvement that I made:
When changing the size, simply given each element a new width percentage. This is also the solution that I learned in the course. 


### Questions that I have 
#### About cache
Through reading documentations on cache, I think cache is done through "back end", because one has to modify header request to the server. So in the front end class, I am not able to set that up, right? 

#### About requestAnimationFrame 
After reading [Leaner, Meaner, Faster Animations with requestAnimationFrame](http://www.html5rocks.com/en/tutorials/speed/animations/) I came to think that rAF might be a solution to the scrolling problem. I modified the code, and tested it. The result was that it worked not well for the project that I am working on. More missing frames than without rAF. So I submitted the version where I didn't use rAF. The following is the code using rAF: 

  ```bash
    var scrollTopValue = 0, 
    scrolled = false; 

    function update() {
      toUpdate();
      scrollTopValue = document.body.scrollTop;
    }

    function movePizzas() {
      scrolled = false;
      var transfromArry = [];
      for (var i = 0; i < itemsNum; i++) {
        var transformValue = 100 * Math.sin((scrollTopValue / 1250) + (i % 5)) - 100 * Math.sin(i % 5);
        var toTransform = 'translate(' + transformValue + 'px)';
        transfromArry.push(toTransform);
      }
      for (var i = 0; i < itemsNum; i++) {
        items[i].style.transform = transfromArry[i];
      }
    }

    function toUpdate() {
      if(!scrolled){
        movePizzas();
        requestAnimationFrame(movePizzas);
      }
      scrolled = true; 
    }
    
    window.addEventListener('scroll', update);
  ```

Can you give me some hint on why this doesn't work well on my computer? 


