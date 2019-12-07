# 实现
<p>实现一级导航标签点击隐藏或显示,并本地缓存</p>

# 原理
<p>使用了_sidebar.md文件进行右侧导航,并且不可动用原结构。</p>
<p>_sidebar.md里所有节点均为一级标题,只是形式上如二级导航。</p>
<p>sidebar-nav在a标签跳转时未刷新,可以保存它和相应子节点状态进行操作。</p>
<p>页面渲染完毕后,监听sidebar-nav的点击事件,本地进行。</p>

### 基本构建
<p>封装一个针对class增删查的工具方法</p>

```javascript
//查询
function getClass(n) {
    if(n.getAttribute){
        var c = n.className;
        if(c === null)return ''
        var arr = c.split(/\s+/);
        //切成数组返回,没class的返[]
        return arr[0] === '' ? [] : arr
    }
}
//添加
function setClass(n, c){
    if(n.getAttribute){
        var oC = n.className;
        oC = oC || '';
        var arr = oC.split(/\s+/);
        if(arr[0] === ''){
            n.setAttribute('class', c);
        }else{
            n.setAttribute('class', oC + ' ' + c);
        }
    }
}
//删除
function reClass(n, c){
    if(n.getAttribute){
        var oC = n.className;
        if(oC.split(' ').indexOf(c) < 0) return false; 
        oC = oC.replace(c, '');
        n.setAttribute('class', oC);
    }
}
```

<p>在docsify的plugins里初始化</p>

```javascript
//初始化一个对象放置状态
var vDom = {};
```
```javascript
plugins: [function(hook, vm) {
    hook.ready(function () {
        //ie9以下不支持document.getElementsByClassName
        var navDom = document.getElementsByClassName('sidebar-nav')[0];
        //顶层dom保存
        vDom.root = navDom;
        if(localStorage.getItem('liArr')){
            //本地有缓存则直接渲染
            vDom.li = localStorage.getItem('liArr').split(',');
            render();
        }else{
            //初始状态
            vDom.li = new Array(navDom.childNodes[0].childNodes.length);
        }
        //点击事件
        navDom.onclick = function (e) {
            //点击的a标签如果下层有ul,则执行
            if(e.target.nextSibling && e.target.nextSibling.nodeName === 'UL'){
                //判断子节点是否有active,有则不隐藏
                if(e.target.nextSibling.innerHTML.indexOf('active') < 0){
                    for(var i = 0; i < navDom.childNodes[0].childNodes.length; i++){
                        //找到对应的dom,状态存到vDom.li数组中
                        if(navDom.childNodes[0].childNodes[i] === e.target.parentNode){
                        vDom.li[i] = vDom.li[i] ? '' : true;
                        localStorage.setItem('liArr', vDom.li)
                        }
                    }
                }
            }
            if(getClass(e.target.parentNode).indexOf('active') > -1) render();
        }
    })
    //路由切换
    hook.doneEach(function () {
        //第一次进来不渲染
        if(!vDom.root) return false;
          //active在子节点, 父节点展开
          for(var i = 0; i < vDom.root.childNodes[0].childNodes.length; i ++ ){
            if(vDom.root.childNodes[0].childNodes[i].innerHTML.indexOf('active') > 0){
              vDom.li[i] = false;
            }
          }
        render()
    })
}]
```
<p>render函数与css</p>

```css
.hidden > ul{
    display: none
}
```
```javascript
function render() {
    //根据sidebar-nav和子节点状态进行隐藏与显示
    for(var i = 0; i < vDom.root.childNodes[0].childNodes.length; i ++){
        if(vDom.li[i]){
            if(getClass(vDom.root.childNodes[0].childNodes[i]).indexOf('hidden') < 0)
                setClass(vDom.root.childNodes[0].childNodes[i], 'hidden')
            }else{
                reClass(vDom.root.childNodes[0].childNodes[i], 'hidden')
        }
    }
}
```
