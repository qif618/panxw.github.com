---
layout: post
category: "other"
title:  "Jekyll安装调试"
tags: [Other]
---



1、安装[Ruby](https://rubyinstaller.org/downloads/)   

2、windows可以下载ZIP安装[RubyGems](https://rubygems.org/pages/download)  

    $ cd {zip-path} 
    $ ruby setup.rb  
<!-- more -->

3、安装Jekyll  

    $ gem install jekyll  

4、安装jekyll-paginate  

    $ gem install jekyll-paginate  


5、报错：  

    Dependency Error: Yikes! It looks like you don't have tzinfo or one of its dependencies installed. In order
      to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is:
      'cannot load such file -- tzinfo' If you run into trouble, you can find helpful resources at
      https://jekyllrb.com/help/!  

则安装：  

    $ gem install tzinfo  

6、报错：  

    C:/Ruby23-x64/lib/ruby/gems/2.3.0/gems/tzinfo-1.2.2/lib/tzinfo/data_source.rb:182:in `rescue in create_default_data_source': No source of timezone data could be found. (TZInfo::DataSourceNotFound)
    Please refer to http://tzinfo.github.io/datasourcenotfound for help resolving this error.  

则安装：  

    $ gem install tzinfo-data

7、_config.yml文件中替换gems

    _config.yml中的gems:
     				- jekyll-paginate
     换成
			     - plugins:
			     	- jekyll-paginate


8、启动jekyll:

    $ cd {local repository}
    $ jekyll serve

9、输入localhost:4000即可访问项目。