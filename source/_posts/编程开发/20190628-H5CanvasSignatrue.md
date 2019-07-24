---
title: H5使用Canvas绘制手写签名
tags:
  - JavaScript
  - Canvas
comments: true
date: 2019-06-28 15:16:00
categories: 编程开发
---

要绘制手写签名，并保存图片似乎只能用Canvas了，且在手机端HTML5还需处理IOS的触底滚动回弹（橡皮筋效果）。

主要技术点为：

- Canvas绘制轨迹、重置
- 手写时阻止网页滚动
- Canvas画布屏幕自适应
- Canvas保存图片

看上去主要还是要克服Canvas。


> 技术栈: Vue

<!--more-->

# Canvas绘制轨迹、重置

```Vue
<template>
  <div class="container">
    <div class="title">
      <span>请手写输入您的签名</span>
      <div v-on:click="handleReset" class="reset">重置</div>
    </div>
    <canvas
      id="canvas"
      width="570"
      height="240"
      v-on:mousedown="handleMouseDown"
      v-on:mouseup="handleMouseUp"
      v-on:mousemove="handleMouseMove"
      v-on:touchstart="handleTouchStart"
      v-on:touchmove="handleTouchMove"
      v-on:touchend="handleTouchEnd"
    >抱歉，您的浏览器不支持电子签名，请更换或升级当前浏览器后重试！</canvas>
  </div>
</template>
<script>
export default {
  name: 'Signature',
  props: {
    onSign: {
      type: Function,
      default: () => {},
    },
    onSaveImage: {
      type: Function,
      default: () => {},
    },
  },
  data() {
    return {
      mouse: {
        current: {
          x: 0,
          y: 0,
        },
        down: false,
      },
    };
  },
  computed: {
    currentMouse() {
      const c = document.getElementById('canvas');
      const rect = c.getBoundingClientRect();

      return {
        x: this.mouse.current.x - rect.left,
        y: this.mouse.current.y - rect.top,
      };
    },
  },
  methods: {
    draw() {
      if (this.mouse.down) {
        const c = document.getElementById('canvas');

        const ctx = c.getContext('2d');

        ctx.clearRect(0, 0, c.width, c.height);

        ctx.lineTo(this.currentMouse.x, this.currentMouse.y);
        // 颜色
        ctx.strokeStyle = '#000';
        // 宽度
        ctx.lineWidth = 2;
        ctx.stroke();
      }
    },
    handleMouseDown(event) {
      this.mouse.down = true;
      this.mouse.current = {
        x: event.pageX,
        y: event.pageY,
      };

      const c = document.getElementById('canvas');
      const ctx = c.getContext('2d');

      ctx.moveTo(this.currentMouse.x, this.currentMouse.y);
    },
    handleMouseUp() {
      this.mouse.down = false;
    },
    handleMouseMove(event) {
      this.mouse.current = {
        x: event.pageX,
        y: event.pageY,
      };

      this.draw(event);
    },
    handleTouchStart(event) {
      const canvas = document.getElementById('canvas');
      const touch = event.touches[0];
      const me = new MouseEvent('mousedown', {
        clientX: touch.clientX,
        clientY: touch.clientY,
      });
      canvas.dispatchEvent(me);
    },
    handleTouchMove(event) {
      const canvas = document.getElementById('canvas');
      const touch = event.touches[0];
      const me = new MouseEvent('mousemove', {
        clientX: touch.clientX,
        clientY: touch.clientY,
      });
      canvas.dispatchEvent(me);
    },
    handleTouchEnd() {
      const canvas = document.getElementById('canvas');
      const me = new MouseEvent('mouseup', {});
      canvas.dispatchEvent(me);
    },
    handleDownload() {
      const canvasElement = document.getElementById('canvas');

      const MIME_TYPE = 'image/png';

      const imgURL = canvasElement.toDataURL(MIME_TYPE);
      // 创建a标签模拟点击触发浏览器下载机制
      const dlLink = document.createElement('a');
      dlLink.download = +new Date();
      dlLink.href = imgURL;
      dlLink.dataset.downloadurl = [MIME_TYPE, dlLink.download, dlLink.href].join(':');

      document.body.appendChild(dlLink);
      dlLink.click();
      document.body.removeChild(dlLink);
    },
    handleReset() {
      const canvas = document.getElementById('canvas');
      // 重置canvas画布
      canvas.width = canvas.width;
      canvas.height = canvas.height;
      this.mouse.current = {
        x: 0,
        y: 0,
      };
    },
  },
  mounted() {
    const c = document.getElementById('canvas');
    const ctx = c.getContext('2d');
    ctx.translate(0.5, 0.5);
    ctx.imageSmoothingEnabled = false;
    this.draw();
  },
};
</script>
<style scoped lang="less">
  .container {
    border: 0.01rem solid #E4E4E4;
    background: #fff;
    border-radius: .04rem;
    padding: .3rem;
    .title {
      display: flex;
      justify-content: space-between;
      align-items: center;
      .reset {
        width: 1.26rem;
        height: 0.6rem;
        line-height: 0.6rem;
        text-align: center;
        border: 0.01rem solid #F8875F;
        color: #F8875F;
        border-radius: 0.04rem;
      }
    }
  }
</style>
```



# 默认事件阻止

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=0">
  <meta name="format-detection" content="telephone=no, email=no" />
  <title>电子签名</title>
  <style>
  @media screen and (max-width: 321px) {
    body {
      font-size:13px
    }
  }

  @media screen and (min-width: 321px) and (max-width:400px) {
    body {
      font-size:14px
    }
  }

  @media screen and (min-width: 400px) {
    body {
      font-size:15px
    }
  }
  </style>
</head>

<body>
  <div id="app-box"></div>
</body>
<script>
  const clientWidth = document.documentElement.clientWidth;
  // 设计图尺寸
  document.documentElement.style.fontSize = clientWidth / 7.5 + 'px';

  const canvas = document.getElementById("canvas");
  //根据设计图中的canvas画布的占比进行设置
  const canvasWidth = Math.floor(clientWidth / 750 * 570);
  const canvasHeight = Math.floor(clientWidth / 750 * 380);
  canvas.setAttribute('width',canvasWidth + 'px');
  canvas.setAttribute('height',canvasHeight + 'px');
    // Prevent scrolling when touching the canvas
  document.body.addEventListener("touchstart", function(e) {
    if (e.target == canvas) {
      e.preventDefault();
    }
  }, { passive: false });
  document.body.addEventListener("touchend", function(e) {
    if (e.target == canvas) {
      e.preventDefault();
    }
  }, { passive: false });
  document.body.addEventListener("touchmove", function(e) {
    if (e.target == canvas) {
      e.preventDefault();
    }
  }, { passive: false });
  // IE浏览器（but H5还有IE吗？？）不支持canvas toBlob()方法的Polyfill
  if (!HTMLCanvasElement.prototype.toBlob) {
    Object.defineProperty(HTMLCanvasElement.prototype, 'toBlob', {
      value: function (callback, type, quality) {
        const canvas = this;
        setTimeout(function() {
          const binStr = atob(canvas.toDataURL(type, quality).split(',')[1]);
          const len = binStr.length, arr = new Uint8Array(len);

          for (let i = 0; i < len; i++ ) {
            arr[i] = binStr.charCodeAt(i);
          }
          callback(new Blob([arr], {type: type || 'image/png'}));
        });
      }
    });
  }
</script>
</html>
```

# Canvas画布屏幕自适应

JS动态设置宽高

```javascript
  const clientWidth = document.documentElement.clientWidth;
  // 设计图尺寸
  document.documentElement.style.fontSize = clientWidth / 7.5 + 'px';

  const canvas = document.getElementById("canvas");
  //根据设计图中的canvas画布的占比进行设置
  const canvasWidth = Math.floor(clientWidth / 750 * 570);
  const canvasHeight = Math.floor(clientWidth / 750 * 380);
  canvas.setAttribute('width',canvasWidth + 'px');
  canvas.setAttribute('height',canvasHeight + 'px');
```

# Canvas保存图片

如果保存图片是直接传给后端，可直接使用 `canvasElement.toBlob()` 方法

```javascript
const canvasElement = document.getElementById('canvas');
// const MIME_TYPE = 'image/png';

// const base64Data = canvasElement.toDataURL(MIME_TYPE);
canvasElement.toBlob((blob) => {
  const fm = new FormData();
  fm.append('file', blob);
  Api.upload(fm).then((data) => {
    if (data && Array.isArray(data) && data.length) {
      const picUrl = data[0].img;
      Api.submit({
        projectId,
        picUrl,
      }).then(() => {
        this.$vux.toast.show({
          text: '提交成功',
          type: 'text',
        });
        return true;
      }).then(() => {
        this.$router.replace({
          path: `/protocol/${projectId}`,
        });
      });
    }
  });
}, 'image/png', 0.8);
```

或转化为base64
```javascript
const canvasElement = document.getElementById('canvas');

const MIME_TYPE = 'image/png';

const imgURL = canvasElement.toDataURL(MIME_TYPE);
// 创建a标签模拟点击触发浏览器下载机制
const dlLink = document.createElement('a');
dlLink.download = +new Date();
dlLink.href = imgURL;
dlLink.dataset.downloadurl = [MIME_TYPE, dlLink.download, dlLink.href].join(':');

document.body.appendChild(dlLink);
dlLink.click();
document.body.removeChild(dlLink);
```

base64编码转blob

```javascript
/**
 * base64编码转blob
 * @param {Base64} base64Data
 * @returns {Blob}
 */
export function base64toBlob(base64Data, type = 'png') {
  const byteString = window.atob(base64Data.split(',')[1]);
  const ab = new window.ArrayBuffer(byteString.length);
  const ia = new window.Uint8Array(ab);
  for (let i = 0; i < byteString.length; i += 1) {
    ia[i] = byteString.charCodeAt(i);
  }
  return new window.Blob([ab], {
    type: `image/${type}`,
  });
}
```

# 参考

- [E-Signature using Canvas](https://codepen.io/yguo/pen/OyYGxQ)