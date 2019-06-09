---
title: " bootstrap+imagesLoaded+masonry+carouse瀑布流、轮播图\t\t"
tags:
  - bootstrap
  - imagesLoaded
  - masonry
url: 1130.html
id: 1130
categories:
  - 前端
date: 2018-04-17 21:51:37
---

介绍
--

masonry是js瀑布流插件，imagesLoaded可实现对图像加载状态的事件处理。选用masonry是因为此框架不仅适用于img标签，可直接用于div标签，可更好地和bootstrap组合使用。Carouse是bootstrap插件可以实现轮播图效果。下面适用于bootstrap3

瀑布流范例
-----

html代码

<div id="masonry-images" class="row">
  <div class="col-md-4 masonry-item">
    <div class="fh5co-blog animate-box">
      <a class="blog-bg" style="background-image: url(images/portfolio-1.jpg);"></a>
    </div>
  </div>
  <div class="col-md-4 masonry-item">
    <div class="fh5co-blog animate-box">
      <a class="blog-bg" style="background-image: url(images/portfolio-1.jpg);"></a>
    </div>
  </div>
  <div class="col-md-4 masonry-item">
    <div class="fh5co-blog animate-box">
      <a class="blog-bg" style="background-image: url(images/portfolio-1.jpg);"></a>
    </div>
  </div>
  <div class="col-md-4 masonry-item">
    <div class="fh5co-blog animate-box">
      <a class="blog-bg" style="background-image: url(images/portfolio-1.jpg);"></a>
    </div>
  </div>
  <div class="col-md-4 masonry-item">
    <div class="fh5co-blog animate-box">
      <a class="blog-bg" style="background-image: url(images/portfolio-1.jpg);"></a>
    </div>
  </div>
  <div class="col-md-4 masonry-item">
    <div class="fh5co-blog animate-box">
      <a class="blog-bg" style="background-image: url(images/portfolio-1.jpg);"></a>
  </div>
</div>

js

<script src="js/bootstrap.min.js"></script>
<script src="js/jquery.min.js"></script>
<script type="text/javascript" src="js/masonry.pkgd.min.js" ></script>
<script type="text/javascript" src="js/imagesloaded.pkgd.min.js"></script>
<script type="text/javascript">
  $(document).ready(
    function() {
      var container = $('#masonry-images.masonry-item');
      var masonryContainer = $('#masonry-images');
      container.imagesLoaded(function(){
        container.fadeIn();
        masonryContainer.masonry({
          itemSelector : '.masonry-item',
          gutter: 0,
          isAnimated: true,
        });
      });
    }
      );
</script>

css

.masonry-item {
  float: left;
}

其中id=masonry-images是流的容器，而masonry-item是流中每一个内容div的class，注意让masonry-item在masonry-images的下级。 js中itemSelector 要填写内容class。 gutter是每个内容框之间的间距，因为上面用了bootstrap，使用的是col-md-4，12/4=3每行显示三个，所以gutter要设置为0，增加了间距会导致一行不能正好放置3个

### 图片加载慢导致排版错乱

按照上面写的：var container = $('#masonry-images.masonry-item');等于是每一个内容的图片加载完成执行一次对这个图片的排版，若下面的图片比上面的先加载完成会出现，下方覆盖上方内容的情况，应以一定途径保证图片加载顺序，否则可使用var container = $('#masonry-images');等于是让整个容器的所有图片加载完成后再统一进行排版。

### imagesLoaded说明

下面转自[https://segmentfault.com/a/1190000007316974](https://segmentfault.com/a/1190000007316974)

// jQuery配置方式
$('#container').imagesLoaded( function() {
  // 图片加载后执行的方法
});
// javaScript配置方式
var imgLoad = imagesLoaded( '#container', function() {
    // 图片加载后执行的方法
});
//下面是全部图片加载的事件流
$('#container').imagesLoaded()
  .always( function( instance ) {
    console.log('图片已全部加载，或被确认加载失败');
  })
  .done( function( instance ) {
    console.log('图片全部加载成功');
  })
  .fail( function( instance ) {
    console.log('图片已全部加载，且至少一个图片加载失败');
  })
  .progress( function( instance, image ) {
    console.log('每张图片加载完');
    var result = image.isLoaded ? 'loaded' : 'broken';
    console.log( '加载结果 ' + result + ' 图片地址 ' + image.img.src );
  });
// javaScript方式
imgLoad.on( 'always', onAlways);

轮播图
---

结合上面的例子，做修改，将第一个内容框的单张图片改成轮播图，首先已经包含了bootstrap.min.js文件，若只需要轮播图，请单独包含carouse.js，下面是一个内容的html

<div class="col-md-4 masonry-item"> 
  <div class="fh5co-blog animate-box"> 
    	<div id="myCarouse-jrtt" class="carousel slide">
              <ol class="carousel-indicators">
                <li data-target="#myCarouse-jrtt" data-slide-to="0" class="active"></li>
                <li data-target="#myCarouse-jrtt" data-slide-to="1"></li>
                <li data-target="#myCarouse-jrtt" data-slide-to="2"></li>
              </ol>
              <div class="carousel-inner">
                <div class="item active">
                  <img src="/pic/1.png" alt="First slide">
                </div>
                <div class="item">
                  <img src="/pic/2.png" alt="Third slide">
                </div>
                <div class="item">
                  <img src="/pic/3.png" alt="Third slide">
                </div>
              </div>
              <a class="left carousel-control" href="#myCarouse-jrtt" role="button" data-slide="prev">
                <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
                <span class="sr-only">Previous</span>
              </a>
              <a class="right carousel-control" href="#myCarouse-jrtt" role="button" data-slide="next">
                <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
                <span class="sr-only">Next</span>
              </a>
            </div>
  </div>
</div>

若有num个图片，indicators区域需要定义好0~(num-1)个，inner区域给出对应的图片，control可不做修改

### 自动轮播

对于上面的代码，myCarouse-jrtt轮播图加上data-ride和data-interval即可，后者为延迟时间单位毫秒，前者为<div id="myCarouse-jrtt" class="carousel slide" data-ride="carousel" data-interval="2000">