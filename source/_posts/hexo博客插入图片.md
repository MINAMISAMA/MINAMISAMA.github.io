---
title: hexo博客插入图片
date: 2021-11-23 10:24:49
tags:
- hexo
- 教程
---

hexo博客的文章虽然是使用markdown的格式和语法进行编辑，但是在插入图片时会发现markdown的方式无法成功。</br>
在这里记录以下hexo中插入图片的方法，参考文章：
[解决Hexo图片无法显示问题](https://blog.csdn.net/weixin_30734435/article/details/98497054?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.essearch_pc_relevant&spm=1001.2101.3001.4242.1)
[hexo引用本地图片无法显示](https://blog.csdn.net/xjm850552586/article/details/84101345)

<!-- more -->

# 安装插件
首先需要安装一个图片路径转换的插件，插件名字是hexo-asset-image
```
cd hexo-local     #本地的网站目录
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

使用这个命令安装插件可以不用进行修改(但是笔者没有亲自测试过)：
```
$ npm install https://github.com/7ym0n/hexo-asset-image --save
```

修改插件中的内容，打开/node_modules/hexo-asset-image/index.js，将内容更换为下面的代码

```js
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
    	var link = data.permalink;
	if(version.length > 0 && Number(version[0]) == 3)
	   var beginPos = getPosition(link, '/', 1) + 1;
	else
	   var beginPos = getPosition(link, '/', 3) + 1;
	// In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
	var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
		if ($(this).attr('src')){
			// For windows style path, we replace '\' to '/'.
			var src = $(this).attr('src').replace('\\', '/');
			if(!/http[s]*.*|\/\/.*/.test(src) &&
			   !/^\s*\//.test(src)) {
			  // For "about" page, the first part of "src" can't be removed.
			  // In addition, to support multi-level local directory.
			  var linkArray = link.split('/').filter(function(elem){
				return elem != '';
			  });
			  var srcArray = src.split('/').filter(function(elem){
				return elem != '' && elem != '.';
			  });
			  if(srcArray.length > 1)
				srcArray.shift();
			  src = srcArray.join('/');
			  $(this).attr('src', config.root + link + src);
			  console.info&&console.info("update link as:-->"+config.root + link + src);
			}
		}else{
			console.info&&console.info("no src attr, skipped...");
			console.info&&console.info($(this));
		}
      });
      data[key] = $.html();
    }
  }
});

```


打开网站根目录下_config.yml，文件，修改下述内容：
```
post_asset_folder: true
```
然后检查以下_config.yml中的url属性是你的hexo仓库名称，否则需要修改为自己的仓库名称。

# 插入图片
在完成了上述的操作后，我们在执行hexo new <name>命令生成文章时会在_post目录下生成一个name.md和一个名称与文章相同的文件夹，我们将图片资源放在文件夹中就可以被调用。</br>

另外使用这个插件来插入图片需要使用以下方式，而不是md语法：
```
{% asset_img test.jpg This is an test image %}
```

“test.jpg”处为图片的文件名称，“This is an test image”处为图片的说明。


