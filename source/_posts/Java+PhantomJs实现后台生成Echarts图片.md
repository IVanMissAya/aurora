---
title: Java+PhantomJs实现后台生成Echarts图片
cover: 'https://static.ivan.fun/blog/phantomJs.jpg'
index_img: 'https://static.ivan.fun/blog/phantomJs.jpg'
tags:
  - Java
categories: '-Java'
abbrlink: 3b11c989
date: 2020-10-09 10:00:00
---
### *目的：实现在java后台直接根据options生成Echarts统计图片*
### *本文实现原理：java调用Runtime.getRuntime().exec()的方式调用phantomjs。然后由phantomjs处理echarts数据,最终生成图片。*

## 第一步：下载并安装PhantomJs
### 下载地址： [http://phantomjs.org/download.html](http://phantomjs.org/download.html)
### 下载完成后配置环境变量
>把解压后的文件夹的bin目录配置到环境变量Path中

![](https://static.ivan.fun/blog/20190724200903573.png)

### 验证结果
>打开cmd窗口，输入以下内容
```
phantomjs --version
```
若出现版本号则配置成功。

## 第二步：准备生成图片相关的脚本：
1. ### *jquery-3.2.1.min.js*  ​ 下载任意jquery.js版本即可
   ### 下载地址: [JQuery官网下载](https://jquery.com/download/)
2. ### *echarts.min.js*  ​ 下载任意jquery.js版本即可
   ### 下载地址: [ECharts官网下载](https://echarts.apache.org/zh/download.html)
3. ### *echarts-convert.js* 
   ### 此脚本为最主要脚本，生成echart图片的主要逻辑代码都在此脚本内。（完整源码如下）
    ```
    (function() {
        var system = require('system');
        var fs = require('fs');
        var config = {
                // define the location of js files
                JQUERY: 'jquery-3.2.1.min.js',
                //ESL: 'esl.js',
                ECHARTS: 'echarts.min.js',
                // default container width and height
                DEFAULT_WIDTH: '600',
                DEFAULT_HEIGHT: '700'
            },
            parseParams, render, pick, usage;

        usage = function() {
            console.log("\nUsage: phantomjs echarts-convert.js -options options -outfile filename -width width -height height" +
                "OR" +
                "Usage: phantomjs echarts-convert.js -infile URL -outfile filename -width width -height height\n");
        };

        pick = function() {
            var args = arguments,
                i, arg, length = args.length;
            for (i = 0; i < length; i += 1) {
                arg = args[i];
                if (arg !== undefined && arg !== null && arg !== 'null' && arg != '0') {
                    return arg;
                }
            }
        };

        parseParams = function() {
            var map = {},
                i, key;
            if (system.args.length < 2) {
                usage();
                phantom.exit();
            }
            for (i = 0; i < system.args.length; i += 1) {
                if (system.args[i].charAt(0) === '-') {
                    key = system.args[i].substr(1, i.length);
                    if (key === 'infile') {
                        // get string from file
                        // force translate the key from infile to options.
                        key = 'options';
                        try {
                            map[key] = fs.read(system.args[i + 1]).replace(/^\s+/, '');
                        } catch (e) {
                            console.log('Error: cannot find file, ' + system.args[i + 1]);
                            phantom.exit();
                        }
                    } else {
                        map[key] = system.args[i + 1].replace(/^\s+/, '');
                    }
                }
            }
            return map;
        };

        render = function(params) {
            var page = require('webpage').create(),
                createChart;

            var bodyMale = config.SVG_MALE;
            page.onConsoleMessage = function(msg) {
                console.log(msg);
            };

            page.onAlert = function(msg) {
                console.log(msg);
            };

            createChart = function(inputOption, width, height, config) {
                var counter = 0;

                function decrementImgCounter() {
                    counter -= 1;
                    if (counter < 1) {
                        console.log(messages.imagesLoaded);
                    }
                }

                function loadScript(varStr, codeStr) {
                    var script = $('<script>').attr('type', 'text/javascript');
                    script.html('var ' + varStr + ' = ' + codeStr);
                    document.getElementsByTagName("head")[0].appendChild(script[0]);
                    if (window[varStr] !== undefined) {
                        console.log('Echarts.' + varStr + ' has been parsed');
                    }
                }

                function loadImages() {
                    var images = $('image'),
                        i, img;
                    if (images.length > 0) {
                        counter = images.length;
                        for (i = 0; i < images.length; i += 1) {
                            img = new Image();
                            img.onload = img.onerror = decrementImgCounter;
                            img.src = images[i].getAttribute('href');
                        }
                    } else {
                        console.log('The images have been loaded');
                    }
                }
                // load opitons
                if (inputOption != 'undefined') {
                    // parse the options
                    loadScript('options', inputOption);
                    // disable the animation
                    options.animation = false;
                }

                // we render the image, so we need set background to white.
                $(document.body).css('backgroundColor', 'white');
                var container = $("<div>").appendTo(document.body);
                container.attr('id', 'container');
                container.css({
                    width: width,
                    height: height
                });
                // render the chart
                var myChart = echarts.init(container[0]);
                myChart.setOption(options);
                // load images
                loadImages();
                return myChart.getDataURL();
            };

            // parse the params
            page.open("about:blank", function(status) {
                // inject the dependency js
                page.injectJs(config.ESL);
                page.injectJs(config.JQUERY);
                page.injectJs(config.ECHARTS);
                var width = pick(params.width, config.DEFAULT_WIDTH);
                var height = pick(params.height, config.DEFAULT_HEIGHT);
                // create the chart
                var base64 = page.evaluate(createChart, params.options, width, height, config);
                fs.write("base64.txt", base64);
                // define the clip-rectangle
                page.clipRect = {
                    top: 0,
                    left: 0,
                    width: width,
                    height: height
                };
                // render the image
                page.render(params.outfile);
                console.log('render complete:' + params.outfile);
                // exit
                phantom.exit();
            });
        };
        // get the args
        var params = parseParams();

        // validate the params
        if (params.options === undefined || params.options.length === 0) {
            console.log("ERROR: No options or infile found.");
            usage();
            phantom.exit();
        }
        // set the default out file
        if (params.outfile === undefined) {
            var tmpDir = fs.workingDirectory + '/tmp';
            // exists tmpDir and is it writable?
            if (!fs.exists(tmpDir)) {
                try {
                    fs.makeDirectory(tmpDir);
                } catch (e) {
                    console.log('ERROR: Cannot make tmp directory');
                }
            }
            params.outfile = tmpDir + "/" + new Date().getTime() + ".png";
        }
        // render the image
        render(params);
    }());
    ```

## 第三步：后端准备及代码编写

1. ### 自己编写常用的测试options，主要为折线图、柱状图、饼图
   饼图示例options
   ```
    {"title":{"text":"渠道图","subtext":"渠道统计","x":"CENTER"},"toolbox": {"feature": {"saveAsImage": {"show": true,}}},"tooltip": {"show": true},"legend": {"data":["直接访问","邮件营销","联盟广告","视频广告","搜索引擎"]}, "series":[{"name":"访问来源","type":"pie","radius": '55%',"center": ['50%', '60%'],"data":[{"value":335, "name":"直接访问"},{"value":310, "name":"邮件营销"},{"value":234, "name":"联盟广告"},{"value":135, "name":"视频广告"},{"value":1548, "name":"搜索引擎"}]}]}
   ```

   ​柱状图示例options
   ```
    {"title":{"text":"销售图","subtext":"销售统计","x":"left"},"toolbox": {"feature": {"saveAsImage": {"show": true,}}},"tooltip": {"show": true},"legend": {"data":['销量']},"xAxis" : [{ "type" : "category","data" : ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]}],"yAxis" : [{"type" : "value"}],"series" : [{"name":"销量","type":"bar","data":[5, 20, 40, 10, 10, 20]}]}
   ```

   折线图示例options
   ```
   {"title":{"text":"资源增长情况","x":"left"},"toolbox":{"feature":{"saveAsImage":{"show":true,"title":"保存为图片","type":"png","lang":["点击保存"]}},"show":true},"tooltip":{"trigger":"axis"},"legend":{"data":["ECS","实例","CPU","MEM"]},"xAxis":[{"boundaryGap":false,"type":"category","data":["2019-03-09","2019-03-02","2019-03-16"]}],"yAxis":[{"type":"value","position":"left","name":"ECS台","axisLine":{"lineStyle":{"color":"#1E90FF"}}},{"type":"value","position":"left","name":"容器实例台","axisLine":{"lineStyle":{"color":"#8A2BE2"}}}],"series":[{"name":"ECS","type":"line","stack":"总量","data":[120,132,101,134,90,230,210]},{"name":"实例","type":"line","stack":"总量","data":[220,182,191,234,290,330,310]},{"name":"CPU","type":"line","stack":"总量","data":[150,232,201,154,190,330,410]},{"name":"MEM","type":"line","stack":"总量","data":[150,232,201,154,190,330,410]}]}
   ```
   ### 这里推荐一个Java生成option的插件 ---- ECharts - Java类库 [Gitee地址](https://gitee.com/free/ECharts)
2. ### 使用Java传cmd命令调用 PhantomJS 生成 echarts 图片（完整源码如下）
   ```
    package com.seed.utils;

    import java.io.BufferedReader;
    import java.io.BufferedWriter;
    import java.io.File;
    import java.io.FileWriter;
    import java.io.IOException;
    import java.io.InputStreamReader;
    import java.util.UUID;

    public class EchartsTest {
        private static final String JSpath = "E:\\testProgram\\EchartsDemo\\tawa\\src\\main\\web\\js\\echarts-convert.js";

        public static void main(String[] args) {
            String options = "{\"title\":{\"text\":\"销售图\",\"subtext\":\"销售统计\",\"x\":\"CENTER\"},\"toolbox\": {\"feature\": {\"saveAsImage\": {\"show\": true,}}},\"tooltip\": {\"show\": true},\"legend\": {\"data\":[\"直接访问\",\"邮件营销\",\"联盟广告\",\"视频广告\",\"搜索引擎\"]}, \"series\":[{\"name\":\"访问来源\",\"type\":\"pie\",\"radius\": '55%',\"center\": ['50%', '60%'],\"data\":[{\"value\":335, \"name\":\"直接访问\"},{\"value\":310, \"name\":\"邮件营销\"},{\"value\":234, \"name\":\"联盟广告\"},{\"value\":135, \"name\":\"视频广告\"},{\"value\":1548, \"name\":\"搜索引擎\"}]}]}";
            String picPath = generateEChart(options);
        }

        /*
        * 主程序
        */
        public static String generateEChart(String options) {
            String dataPath = writeFile(options);
            String fileName = UUID.randomUUID().toString() + ".png";
            String path = "D:/temp/Echart/" + fileName;

            try {
                File file = new File(path); //文件路径

                if (!file.exists()) {
                    File dir = new File(file.getParent());
                    dir.mkdirs();
                    file.createNewFile();
                }

                String cmd = "phantomjs " + JSpath + " -infile " + dataPath +
                    " -outfile " + path; //生成命令行
                Process process = Runtime.getRuntime().exec(cmd);
                BufferedReader input = new BufferedReader(new InputStreamReader(
                            process.getInputStream()));
                String line = "";

                while ((line = input.readLine()) != null) {
                }

                input.close();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
            }

            return path;
        }

        /*
        *
        * options生成文件存储
        */
        public static String writeFile(String options) {
            String dataPath = "D:\\chartData\\data" +
                UUID.randomUUID().toString().substring(0, 8) + ".json";

            try {
                /* option写入文本文件 用于执行命令*/
                File writename = new File(dataPath);

                if (!writename.exists()) {
                    File dir = new File(writename.getParent());
                    dir.mkdirs();
                    writename.createNewFile(); // 
                }

                BufferedWriter out = new BufferedWriter(new FileWriter(writename));
                out.write(options);
                out.flush(); // 把缓存区内容压入文件
                out.close(); // 最后关闭文件
            } catch (IOException e) {
                e.printStackTrace();
            }

            return dataPath;
        }
    }
   ```
   ### 生成的图片输出到了上述代码中定义的文件夹内，结果如图
   ![](https://static.ivan.fun/blog/2019072420091975.png)