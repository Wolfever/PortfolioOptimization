## Welcome to review my code! 

Here is a list of explanations for what I have done to optimize the index.html and main.js.

### Part 1: How I optimized the index.html 

#### Step 1: Compress all the pictures

When I test the original webpage on [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/?hl=zh-CN), it warns me that the pictures are too big. So I used grunt to minify the pictures, and used an [online picture compressing tool](http://compressjpeg.com/zh/) to further compress it. 

#### Step 2: Inline all the CSS and Js files 

PageSpeed Insights also told me to inline all the CSS and JS files. So I: 



### Part 2: How I optimized main.js 
#### Step 1: Create just enough pizzas on the background
The final code looks like the following 

  ```bash
  var itemsNum = Math.floor((window.innerHeight + 100) / 256) * 8;
  for (var i = 0; i < itemsNum; i++) {
    var elem = document.createElement('img');
    ......
    document.querySelector("#movingPizzas1").appendChild(elem);
  }
  ```
#### Step 2: Optimize function updatePositions()

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

#### Step 3: Optimize function changePizzaSizes(size) 

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
When changing the size, simply given each element a new width percentage. 




## Website Performance Optimization portfolio project

Your challenge, if you wish to accept it (and we sure hope you will), is to optimize this online portfolio for speed! In particular, optimize the critical rendering path and make this page render as quickly as possible by applying the techniques you've picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).

To get started, check out the repository and inspect the code.

### Getting started

####Part 1: Optimize PageSpeed Insights score for index.html

Some useful tips to help you get started:

1. Check out the repository
1. To inspect the site on your phone, you can run a local server

  ```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080
  ```

1. Open a browser and visit localhost:8080
1. Download and install [ngrok](https://ngrok.com/) to the top-level of your project directory to make your local server accessible remotely.

  ``` bash
  $> cd /path/to/your-project-folder
  $> ./ngrok http 8080
  ```

1. Copy the public URL ngrok gives you and try running it through PageSpeed Insights! Optional: [More on integrating ngrok, Grunt and PageSpeed.](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

Profile, optimize, measure... and then lather, rinse, and repeat. Good luck!

####Part 2: Optimize Frames per Second in pizza.html

To optimize views/pizza.html, you will need to modify views/js/main.js until your frames per second rate is 60 fps or higher. You will find instructive comments in main.js. 

You might find the FPS Counter/HUD Display useful in Chrome developer tools described here: [Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).

### Optimization Tips and Tricks
* [Optimizing Performance](https://developers.google.com/web/fundamentals/performance/ "web performance")
* [Analyzing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp.html "analyzing crp")
* [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/optimizing-critical-rendering-path.html "optimize the crp!")
* [Avoiding Rendering Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css.html "render blocking css")
* [Optimizing JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript.html "javascript")
* [Measuring with Navigation Timing](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/measure-crp.html "nav timing api"). We didn't cover the Navigation Timing API in the first two lessons but it's an incredibly useful tool for automated page profiling. I highly recommend reading.
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/eliminate-downloads.html">The fewer the downloads, the better</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer.html">Reduce the size of text</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization.html">Optimize images</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching.html">HTTP caching</a>

### Customization with Bootstrap
The portfolio was built on Twitter's <a href="http://getbootstrap.com/">Bootstrap</a> framework. All custom styles are in `dist/css/portfolio.css` in the portfolio repo.

* <a href="http://getbootstrap.com/css/">Bootstrap's CSS Classes</a>
* <a href="http://getbootstrap.com/components/">Bootstrap's Components</a>
