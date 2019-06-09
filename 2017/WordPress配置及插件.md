title: " WordPress配置及插件\t\t"
tags:
  - wordpress
  - 插件
url: 241.html
id: 241
categories:
  - 其他
date: 2017-11-09 20:16:13
---
常用插件
----

* Enlighter - Customizable Syntax Highlighter

代码高亮插件，安装启动后在根目录下有Enlighter设置菜单。

Theme：显示界面风格

Default Language：默认语言，设置为自己常用的语言，每次插入编码可不用再支持代码列表挑选

插入代码方式：在文章编辑界面，可视化界面新增了两个工具图标 "enlighter code insert"插入代码，选择以后弹出窗口进行配置 "enlighter code settings"配置当前代码段。必须先选择到代码段，否则此按钮不可使用

* WP-Optimize

WP数据库优化使用，安装完成后再根目录增加WP-Optimize配置页面。 此工具可以清理文章版本历史等，提高数据库检索速度。

> 使用此工具请注意备份数据库

进入配置页面可直接点击“Run all selected”执行优化，下方有具体优化项目。 设置页面，可以进行自动定时优化的设置，建议在网站流量较小时进行自动优化。 

* Google XML Sitemaps

建立网站地图。 安装完成后再设置菜单增加XML-Sitmap设置页面。 进入页面可直接默认配置，点击最下方的保存设置，可在http://techieliang.com/sitemap.xml看到网站地图 

* Featured Image From URL

特色图片支持外链。 安装完成后根目录增加设置页面。 

* Shortcodes Ultimate(终极简码)

由于wp在页面和文档编辑默认使用静态html不支持php等指令，使用此工具可以在编辑文档时插入多种短代码，有列表、框、插图、按钮、动画、页面嵌入等多种短代码。 安装完成后再根目录添加“简码”设置页面。 在文档编辑页面的“添加媒体”右侧增加了“插入简码”按钮，可插入指定短代码。 

* All In One WP Security

安全插件、数据库备份、暴力破解保护、文件权限推荐

配置
--

### 更改数据库地址

更换主机以后，需要更改数据库地址，只需要修改根目录下的“wp-config.php”文件即可
```
// ** MySQL 设置 - 具体信息来自您正在使用的主机 */
/** WordPress数据库的名称 */
define('DB\_NAME', 'ttttttt\_blog');

/** MySQL数据库用户名 */
define('DB_USER', 'ttttttttt');

/** MySQL数据库密码 */
define('DB_PASSWORD', 'tstatstat');

/** MySQL主机 */
define('DB_HOST', 'techieliang.com');

/** 创建数据表时默认的文字编码 */
define('DB_CHARSET', 'utf8');

/** 数据库整理类型。如不确定请勿更改 */
define('DB_COLLATE', '');
```
### 后台登陆地址更改

使用Login LockDown插件只能保护防止密码被暴力破解，但在别有用心的在不断的扫描Wordpress登陆入口…… 简单的办法：使用Stealth Login Page、Protected wp-login等插件 手动修改 找到 wp-includes\\functions.php文件 增加如下代码
```
add_action('login_enqueue_scripts','login_protection');
function login_protection(){
if($_GET['a1'] != 'a2')header('Location: http://techieliang.com/');
}
```
注意：增加的代码段要在 <?php 和>之间，同时不要破坏原有的function a1、a2及http://techieliang.com/均可修改，网址内容为当后台地址输入错误是网页跳转到何处，可以为其他网址或者网站首页。 更改完成后，后台网址改为： http://XXXX.XXXX.XXX/wp-login.php?a1=a2

### 版权声明添加

在主题的single.php文件 <header>为文章头 <article>为文章内容 可在需要的位置添加声明内容，范例：
```
<div  class=”copyright” style="color:red;">  若无注明，<a href=”http://www.techieliang.com/” title=”Techie亮博客”>Techie亮博客</a>文章均为原创，转载请以链接形式标明本文标题和地址<br/> 本文标题：<?php the\_title(); ?><br/> 本文地址：<a href=”<?php the\_permalink(); ?>” title=”<?php the\_title(); ?>”><?php the\_permalink(); ?></a>  <p></p> </div>
```
### RSS订阅Feed地址

根据固定连接的配置判断，若为纯数字(朴素)(http://techieliang.com/?p=123)为默认结构，其余为其他结构 默认结构地址：http://www.techieliang.com/?feed=rss2?? 注意=后面为版本号 其他结构地址：http://www.techieliang.com/feed/ 具体文章、标签、分类、页面、评论等地址： 默认结构：原始地址&feed=rss2 其他地址：原始地址/feed

### 无插件实现文章索引-单标题索引

效果类似于本文最上的目录效果 在function.php文件php语言段的末尾添加，一般最后添加即可

> 网上搜到的，锚点使用的name，经测试索引无效，点击目录无法跳转，改为id后可正常使用
```
function article_index($content) {
    $matches = array();
    $ul_li = '';
    $r = "/<h2>(\[^<\]+)<\\/h2>/im";
    if(preg\_match\_all($r, $content, $matches)) {
      foreach($matches\[1\] as $num => $title) {
        $content = str_replace($matches\[0\]\[$num\], '<h3 id="title-'.$num.'">'.$title.'</h3>', $content);
        $ul_li .= '<li><a href="#title-'.$num.'" title="'.$title.'">'.$title."</a></li>\\n";
      }
      $content = "\\n<div id=\\"article-index\\" class=\\"article-index hidden-xs\\">
      <strong id=\\"title\\">目录</strong>
      <ul id=\\"index-ul\\" class=\\"index-ul\\">\\n" . $ul_li . "</ul>
      </div>\\n" . $content;
    }
    return $content;
}
add\_filter( "the\_content", "article_index" );
```
注意$r = "/<h2>(\[^<\]+)<\\/h2>/im";此行的h2是二级目录，可以改为其他的，若修改请同时修改文本替换内容：“'<h3 id="title-'.$num.'">'.$title.'</h3>'”这里的h3 在主题的style.css文件添加：
```
#article-index {
    -moz-border-radius: 6px 6px 6px 6px;
    border: 1px solid #DEDFE1;
    float: right;
    margin: 0 0 15px 15px;
    padding: 0 6px;
    width: 200px;
    line-height: 23px;
}
#article-index strong {
    border-bottom: 1px dashed #DDDDDD;
    display: block;
    line-height: 30px;
    padding: 0 4px;
}
#index-ul {
    margin: 0;
    padding-bottom: 10px;
}
#index-ul li {
    background: none repeat scroll 0 0 transparent;
    list-style-type: disc;
    padding: 0;
    margin-left: 20px;
}
```
### 无插件实现文章索引-多级标题索引

下面以2/3两级标题索引为例， 根据上述程序修改，首先要找到多个标题，所以要修改正则表达式：
```
$r = "/<h\[23\]>(.*)<\\/h\[23\]>/im";
```
同时因为多个级别，要区分不同级别，需要取每个标题最开头的3个字母，判断是"<h2"还是"<h3" 最终程序如下：
```
function article_index($content) {
    $matches = array();
    $ul_li = '';
    $r = "/<h\[23\]>(.*)<\\/h\[23\]>/im";
    $h2_num = 0;
    $h3_num = 0;
    if(preg\_match\_all($r, $content, $matches)) {
        foreach($matches\[1\] as $num => $title) {
            $header_3text = substr($matches\[0\]\[$num\], 0, 3);//取前三个字母
            if($header_3text == "<h2"){
                $h2_num += 1;
                $h3_num = 0;//出现一个新的h2标题，下属的h3编号归零
                $content = str\_replace($matches\[0\]\[$num\], '<h2 id="title-'.$num.'">'.$h2\_num.'. '.$title.'</h2>', $content);
                $ul\_li .= '<li><a href="#title-'.$num.'" title="'.$title.'">'.$h2\_num.'. '.$title."</a></li>\\n";
            }else if($header_3text == "<h3"){
                $h3_num += 1;
                $content = str\_replace($matches\[0\]\[$num\], '<h3 id="title-'.$num.'">'.$h2\_num.'.'.$h3_num.'. '.$title.'</h3>', $content);
                $ul\_li .= '<li>&emsp;<a href="#title-'.$num.'" title="'.$title.'">'.$h2\_num.'.'.$h3_num.'. '.$title."</a></li>\\n";
            }   
        }
        $content = "\\n<div id=\\"article-index\\" class=\\"article-index hidden-xs\\">
        <strong id=\\"title\\">文章目录</strong>
        <ul id=\\"index-ul\\" class=\\"index-ul\\">\\n" . $ul_li . "</ul>
        </div>\\n" . $content;
    }
    return $content;
}
add\_filter( "the\_content", "article_index" );
```
css文件与单标题相同。 此处还引入了变量h2_num和h3_num主要用于计数，分别统计是h2标题数量和h3标题数量，同时自动给标题添加序号 序号格式:"x.y. "若需要其他格式可自行在content和ul_li修改，其中content为文中正文标题的格式，ul_li为目录区域标题显示的格式。 由于目录显示了多个级别的标题，为了显示清除，在h3的ul_li的最前面加了一个全角空格emsp;

### 添加禁止点击的菜单

菜单页面-自定义连接，随便写个链接地址和需要的名字-添加 然后把新添加的菜单项的链接地址全部删掉，包括http://也删掉，这样此菜单项只会显示，但不具有点击效果 再添加其他菜单，可以拖拽到其下方右侧，实现多层菜单