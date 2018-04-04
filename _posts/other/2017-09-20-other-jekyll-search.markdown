---
layout: post
category: "other"
title:  "Jekyll站内搜索"
tags: [Other]
---


> 使用simply-jekyll-serch做站内搜索

- 合适位置放搜索框，id为search-query
- 在项目跟目录放一个search.json文件，内容如下  
    {% raw %}
	---
	layout: null  
	---  

	[  
		  {% for post in site.posts %}  
		{  
		  "title": "{{ post.title | escape }}",  
		  "category" : "{{ post.category }}",  
		  "tags" : "{{ post.tags | join: ', ' }}",  
		  "url"  : "{{ site.baseurl }}{{ post.url }}",  
		  "date" : "{{ post.date }}"  
		} {% unless forloop.last %},{% endunless %}  
		  {% endfor %}  
	]  

	{% endraw %}


- 引入simple-jekyll-search.min.js

- 合适位置放一个id为results的ul

<!-- more -->

> 搜索代码  

	<script type="text/javascript">
	  $(function() {
		  SimpleJekyllSearch({
				searchInput: document.getElementById('search-query'),
				resultsContainer: document.getElementById('results'),
				json: '/search.json',
				searchResultTemplate: '<li><a href="{url}" title="{desc}">{title}</a></li>', // 文章列表模板
				noResultsText: '没有搜索到内容', 
				limit: 10, // 返回最大文章数
				fuzzy: false // 是否模糊匹配
	     });
		 });
	</script>





>点击search面板外的空白处，面板消失    

	$(document).bind("click",function(e){//点击空白处，信息面板消失  
	  var target  = $(e.target);  
	  if(target.closest("#search").length == 0){  
		  $('#search-query').val("");  
		   $('#results').html("");
	  }