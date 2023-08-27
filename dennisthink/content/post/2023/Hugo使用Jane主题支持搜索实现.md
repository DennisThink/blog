---
title: "Hugo使用Jane主题支持搜索实现"
date: 2023-01-02T21:38:52+08:00
lastmod: 2023-01-02T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["hugo","jane search"]
comment: true
mathjax: false
---

操作系统：Windows7 64Bit Ultimate版本。


## 1. 安装Hugo

从hugo官网下载Hugo并安装。

[Hugo官网](https://gohugo.io/)


## 2. 添加Hugo.exe 到PATH路径

测试

执行:

```console 
hugo version
```

显示:

![Hugo_version.png)](https://www.dennisthink.com/image/2023/01/Hugo_version.png)


## 3. 新建站点

例如在目录 Github/searchLast下建立search,其中search是你网站的名字。

```console
hugo new site  search
```

执行完成后使用tree命令查看文件路径:
```
tree
```
显示:

![Hugo_new_site_folder_show.png](https://www.dennisthink.com/image/2023/01/Hugo_new_site_folder_show.png)

## 4. 安装jane主题

执行下列的命令安装

```console
git clone https://github.com/xianmin/hugo-theme-jane.git themes/jane
```
执行命令显示:

![Install_jane_theme.png](https://www.dennisthink.com/image/2023/01/Install_jane_theme.png)

安装成功显示:

![Install_jane_theme_succeed.png](https://www.dennisthink.com/image/2023/01/Install_jane_theme_succeed.png)

## 5. 将GitHub\searchLast\search\themes\jane\exampleSite\目录下的config.toml拷贝到GitHub\searchLast\search\目录

执行 hugo server
![Run_hugo_server_first.png](https://www.dennisthink.com/image/2023/01/Run_hugo_server_first.png)

访问主页面
![Jane_site_demo.png](https://www.dennisthink.com/image/2023/01/Jane_site_demo.png)

## 6.  在themes/jane/layouts/目录下添加index.json,内容如下

```json
{{- $.Scratch.Add "index" slice -}}
{{- range .Site.RegularPages -}}
    {{- $.Scratch.Add "index" (dict "title" .Title "tags" .Params.tags "categories" .Params.categories "contents" .Plain "permalink" .Permalink) -}}
{{- end -}}
{{- $.Scratch.Get "index" | jsonify -}}
```

## 7. 在GitHub\searchLast\search\目录的config.toml文件末尾增加

```console
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

## 8. 在GitHub\searchLast\search\themes\jane\layouts\_default\目录下添加search.html,内容如下

search.html
```html
<!DOCTYPE html>
<html lang="{{ $.Site.LanguageCode | default " en" }}" itemscope itemtype="http://schema.org/WebPage">

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    {{ partial "head.html" . }}
    <header id="header" class="header container">
        {{ partial "header.html" . }}
    </header>
</head>

<body>
  <div id="mobile-panel">
    <main id="main" class="main bg-llight wallpaper">
        <div class="content-wrapper">
          <div id="content" class="content container">
            <article class="post bg-white">
                        <section>
                            <div>
                                <div id="search-results">
                                    <h3 style="text-align:center">Matching pages</h3>
                                </div>
                            </div>
                        </section>
                            <script id="search-result-template" type="text/x-js-template">
                                <section>
                                <hr align=center>
                                <div id="summary-${key}">
                                <a href="${link}" style="color:red"><u>${title}</u></a>
                                <p>${snippet}</p>
                                <p>${ isset tags }<p>Tags: ${tags}</p>${ end }<p>
                                <p>${ isset categories }<p>Categories: ${categories}</p>${ end }<p>
                              </div>
                            </section>
                            </script>
                            <script src="/js/jquery-3.3.1.min.js"></script>
                            <script src="https://cdnjs.cloudflare.com/ajax/libs/fuse.js/3.2.0/fuse.min.js"></script>
                            <script
                                src="https://cdnjs.cloudflare.com/ajax/libs/mark.js/8.11.1/jquery.mark.min.js"></script>
                            <script src="{{ "js/search.js" | absURL }}"></script>
                          </article>
          </div>
        </div>
    </main>
    <footer id="footer" class="footer">
      {{ partial "footer.html" . }}
  </footer>

  <div class="back-to-top" id="back-to-top">
      <i class="iconfont">
          {{/* icon up */}}
          {{ partial "svg/up.svg" }}
      </i>
  </div>
</div>
</body>
</html>
```

### 9. 修改 GitHub\searchLast\search\themes\themes/jane/partials/目录下的header.html
添加如下内容到```{{ if .Site.Params.search.google.enable }}```前面

```html
<li class="menu-item">
    <a class="menu-item-link">
    <form action="/search" method="GET">
        <input type="search" name="s" id="search-query" placeholder="">
        <button type="submit">search</button>
    </form>
    </a>
</li>
```


### 10. 在GitHub\searchLast\search\content目录下增加search.md，内容如下
```m
---
title: "搜索功能"
sitemap:
  priority : 0.1
layout: "search"
---
```

### 11. 在 GitHub\searchLast\search\themes\jane\static/js/目录下增加search.js 
内容如下:
```js
summaryInclude=60;
var fuseOptions = {
  shouldSort: true,
  includeMatches: true,
  threshold: 0.0,
  tokenize:true,
  location: 0,
  distance: 100,
  maxPatternLength: 32,
  minMatchCharLength: 1,
  keys: [
    {name:"title",weight:0.8},
    {name:"contents",weight:0.5},
    {name:"tags",weight:0.3},
    {name:"categories",weight:0.3}
  ]
};


var searchQuery = param("s");
if(searchQuery){
  $("#search-query").val(searchQuery);
  console.log("Search:",searchQuery)
  executeSearch(searchQuery);
}else {
  $('#search-results').append("<p>Please enter a word or phrase above</p>");
}



function executeSearch(searchQuery){
  $.getJSON( "/index.json", function( data ) {
    var pages = data;
    var fuse = new Fuse(pages, fuseOptions);
    var result = fuse.search(searchQuery);
    console.log({"matches":result});
    if(result.length > 0){
      populateResults(result);
    }else{
      $('#search-results').append("<p>No matches found</p>");
    }
  });
}

function populateResults(result){
  $.each(result,function(key,value){
    var contents= value.item.contents;
    var snippet = "";
    var snippetHighlights=[];
    var tags =[];
    if( fuseOptions.tokenize ){
      snippetHighlights.push(searchQuery);
    }else{
      $.each(value.matches,function(matchKey,mvalue){
        if(mvalue.key == "tags" || mvalue.key == "categories" ){
          snippetHighlights.push(mvalue.value);
        }else if(mvalue.key == "contents"){
          start = mvalue.indices[0][0]-summaryInclude>0?mvalue.indices[0][0]-summaryInclude:0;
          end = mvalue.indices[0][1]+summaryInclude<contents.length?mvalue.indices[0][1]+summaryInclude:contents.length;
          snippet += contents.substring(start,end);
          snippetHighlights.push(mvalue.value.substring(mvalue.indices[0][0],mvalue.indices[0][1]-mvalue.indices[0][0]+1));
        }
      });
    }

    if(snippet.length<1){
      snippet += contents.substring(0,summaryInclude*2);
    }
    //pull template from hugo templarte definition
    var templateDefinition = $('#search-result-template').html();
    //replace values
    var output = render(templateDefinition,{key:key,title:value.item.title,link:value.item.permalink,tags:value.item.tags,categories:value.item.categories,snippet:snippet});
    $('#search-results').append(output);

    $.each(snippetHighlights,function(snipkey,snipvalue){
      $("#summary-"+key).mark(snipvalue);
    });

  });
}

function param(name) {
    return decodeURIComponent((location.search.split(name + '=')[1] || '').split('&')[0]).replace(/\+/g, ' ');
}

function render(templateString, data) {
  var conditionalMatches,conditionalPattern,copy;
  conditionalPattern = /\$\{\s*isset ([a-zA-Z]*) \s*\}(.*)\$\{\s*end\s*}/g;
  //since loop below depends on re.lastInxdex, we use a copy to capture any manipulations whilst inside the loop
  copy = templateString;
  while ((conditionalMatches = conditionalPattern.exec(templateString)) !== null) {
    if(data[conditionalMatches[1]]){
      //valid key, remove conditionals, leave contents.
      copy = copy.replace(conditionalMatches[0],conditionalMatches[2]);
    }else{
      //not valid, remove entire section
      copy = copy.replace(conditionalMatches[0],'');
    }
  }
  templateString = copy;
  //now any conditionals removed we can do simple substitution
  var key, find, re;
  for (key in data) {
    find = '\\$\\{\\s*' + key + '\\s*\\}';
    re = new RegExp(find, 'g');
    templateString = templateString.replace(re, data[key]);
  }
  return templateString;
}
```

从网络上下载  ***jquery-3.3.1.min.js*** ,并将其拷贝到 GitHub\searchLast\search\themes\jane\static/js/目录下。

### 11. 重新启动hugo server

访问: [测试页面](http://127.0.0.1:1313/)

页面显示:
![](https://www.dennisthink.com/image/2023/01/Add_Search_button_succeed.png)



### 12. 增加两篇文章


在 GitHub\searchLast\search 目录
执行

```console
hugo new post/first.md
```

```console
hugo new post/second.md
```


### 13. 修改first.md的 draft: true 为 draft: false 并在文件末尾增加 
this is my first blog

### 13. 修改second.md的 draft: true 为 draft: false 并在文件末尾增加 
this is my second blog

### 14. 执行 依次执行 hugo 和 hugo server 


### 15. 访问 http://localhost:1313/post/

查找first
![prepare_search.png](https://www.dennisthink.com/image/2023/01/prepare_search.png)

显示查找结果
![Search_result_show.png](https://www.dennisthink.com/image/2023/01/Search_result_show.png)





















