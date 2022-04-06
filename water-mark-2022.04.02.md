#### 水印生成
1. 方案：生成背景图片铺满整个页面<br/>
(1). 采用svg画图，写为行内样式，有些字符例如<>要进行转义<br/>
```
  <svg width="150" height="150" xmlns="http://www.w3.org/2000/svg"><text y="15" font-weight="lighter" fill="#00f" transform="rotate(-30 120 0)">bodyfu</text></svg>
```
(2). canvas画图，生成base64链接，可以自定义类名，在伪类上添加水印
```
function createImgBase(options) {
  const canvas = document.createElement('canvas');
  const text = options.content;
  // 因为要实现文字交错效果，所以这里生成两倍宽度的图片
  canvas.width = options.width * 2;
  canvas.height = options.height;
  const ctx = canvas.getContext('2d');
  if (ctx) {
    // X轴阴影距离，负值表示往上，正值表示往下
    ctx.shadowOffsetX = 2;
    // Y轴阴影距离，负值表示往左，正值表示往右
    ctx.shadowOffsetY = 2;
    // 阴影的模糊程度
    ctx.shadowBlur = 2;
    ctx.font = options.font;
    ctx.fillStyle = options.color;
    ctx.rotate(options.rotateDegree);
    ctx.textAlign = 'left';
    ctx.fillText(text, options.x, options.y);
  }
  return canvas.toDataURL('image/png');
}



/**
 * 生成水印
 * @param {String} className 水印类名
 * @param {Number} width 宽度
 * @param {Number} height 高度
 * @param {String} content 内容
 * @param {String} font 字体
 * @param {String} color 自定义样式: 如字体颜色(使用RGBA)
 * @param {Number} rotate 翻转角度
 * @param {String} position 水印定位方式
 * @param {Number} top 距离顶部位置
 * @param {Number} left 距离左部位置
 * @param {Number} zIndex 水印层级
 */
function genWaterMark({
  className = 'watermarked',
  width = 150,
  height = 150,
  content = '水印',
  font = '14px PingFang SC, sans-serif',
  color = 'rgba(156, 162, 169, 0.3)',
  rotate = -14,
  position = 'absolute',
  top = 0,
  left = 0,
  zIndex = 1000,
}) {
  const option = {
    width,
    height,
    content,
    font,
    color,
    rotateDegree: (rotate * Math.PI) / 180,
  };

  // 为了实现交错水印的效果
  const dataUri1 = createImgBase({
    ...option,
    x: 50,
    y: 140,
  });
  let styleEl = document.createElement('style');
  styleEl.innerHTML = `.${className}:after
  {
    content: '';
    display: block;
    width: 100%;
    height: 100%;
    ${top || top === 0 ? `top: ${top}px;` : ''}
    ${left || left === 0 ? `left: ${left}px;` : ''}
    background-repeat: repeat;
    pointer-events: none;
    ${position ? `position: ${position}` : ''};
    ${zIndex ? `z-index:${zIndex}` : ''};
    background-image: url(${dataUri1});
    background-size: ${option.width * 2}px ${option.height}px;
  }`;
  document.head.appendChild(styleEl);
}
// 调用生成水印函数,添加了一个伪类在watermask类名上
genWaterMark({
  className: 'waterMask',
  content: '水印内容'
});
// 在需要展示水印的元素上添加waterMask类名即可

```
2. svg了解：
<a href="https://www.ruanyifeng.com/blog/2018/08/svg.html">svg介绍</a>
3. svg可以被img标签或者当做样式中的背景图去使用
```
<img src="xxx.svg">
background-image: url(./static_files/water.svg);
```