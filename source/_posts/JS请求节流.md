---
title: JS请求节流
cover: 'https://static.ivan.fun/blog/js_index.jpg'
index_img: 'https://static.ivan.fun/blog/js_index.jpg'
tags:
  - JavaScript
categories: -JavaScript
abbrlink: 12c97b7c
date: 2020-04-26 09:00:00
---
# JS请求节流
### 不废话 撸代码
1. 节流器
```
// 对函数进行 节流
function throttle (fn, interval = 500) {
  let timer = null;
  let firstTime = true;

  return function () {
    let args = arguments;
    if (firstTime) {
      // 第一次加载
      fn.apply(this, args);
      return firstTime = false;
    }
    if (timer) {
      // 定时器正在执行中，跳过
      return;
    }
    timer = setTimeout(() => {
      clearTimeout(timer);
      timer = null;
      fn.apply(this, args);
    }, interval);
  };
}
```

2. 初始化节流器
```
const throttleFunc=throttle(function (field) {
        var loadIndex = layer.load(1);
        $.ajax({
            type: 'POST',
            url: '/cms/teachers/saveTeacherInfo',
            async: false,
            data: data.field,
            success: function (result) {
                if (result.status==1) {
                    layer.close(loadIndex);
                    layer.msg('保存成功');
                    canJump = 1;
                    if (!isJump) {
                        $('.cancel').trigger('click');
                    }
                } else {
                    $('button[lay-filter="saveTeacherInfo"]').attr("disabled", false).removeClass("layui-disabled");

                    layer.close(loadIndex);
                    layer.msg(result.msg);
                }
            }
        });
        return false;
    },1000);
```

3. 使用节流器
```
throttleFunc(data.field);
```