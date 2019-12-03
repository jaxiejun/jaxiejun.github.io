# 实现
<p>实现一级导航标签点击隐藏或显示,并本地缓存</p>

# 原理
<p>使用了_sidebar.md文件进行右侧导航,并且不可动用原结构。</p>
<p>_sidebar.md里所有节点均为一级标题,只是形式上如二级导航。</p>
<p>sidebar-nav在a标签跳转时未刷新,可以保存它和相应子节点状态进行操作。</p>
<p>页面渲染完毕后,监听sidebar-nav的点击事件,本地进行。</p>

### 基本构建
<p>封装一个针对class增删查的工具方法</p>
<code>
<i>查询</i>
function getClass(n) {
    if(n.getAttribute){
        var c = n.className;
        if(c === null)return ''
        var arr = c.split(/\s+/);
        <i>切成数组返回,没class的返[]</i>
        return arr[0] === '' ? [] : arr
    }
}
<i>添加</i>
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
<i>删除</i>
function reClass(n, c){
    if(n.getAttribute){
        var oC = n.className;
        if(oC.split(' ').indexOf(c) < 0) return false; 
        oC = oC.replace(c, '');
        n.setAttribute('class', oC);
    }
}
</code>
<p>在docsify的plugins里初始化</p>
<code>
<i>初始化一个对象放置状态</i>
var vDom = {};
</code>
<code>
plugins: [function(hook, vm) {
    hook.ready(function () {
        <i>ie9以下不支持document.getElementsByClassName</i>
        var navDom = document.getElementsByClassName('sidebar-nav')[0];
        <i>顶层dom保存</i>
        vDom.root = navDom;
        if(localStorage.getItem('liArr')){
            <i>本地有缓存则直接渲染</i>
            vDom.li = localStorage.getItem('liArr').split(',');
            render();
        }else{
            <i>初始状态</i>
            vDom.li = new Array(navDom.childNodes[0].childNodes.length);
        }
        <i>点击事件</i>
        navDom.onclick = function (e) {
            <i>点击的a标签如果下层有ul,则执行</i>
            if(e.target.nextSibling && e.target.nextSibling.nodeName === 'UL'){
                <i>判断子节点是否有active,有则不隐藏</i>
                if(e.target.nextSibling.innerHTML.indexOf('active') < 0){
                    for(var i = 0; i < navDom.childNodes[0].childNodes.length; i++){
                        <i>找到对应的dom,状态存到vDom.li数组中</i>
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
    <i>路由切换</i>
    hook.doneEach(function () {
        <i>第一次进来不渲染</i>
        if(!vDom.root) return false;
        render()
    })
}]
</code>
<p>render函数与css</p>
<code>
.hidden > ul{
    display: none
}
</code>
<code>
function render() {
    <i>根据sidebar-nav和子节点状态进行隐藏与显示</i>
    for(var i = 0; i < vDom.root.childNodes[0].childNodes.length; i ++){
        if(vDom.li[i]){
            if(getClass(vDom.root.childNodes[0].childNodes[i]).indexOf('hidden') < 0)
                setClass(vDom.root.childNodes[0].childNodes[i], 'hidden')
            }else{
                reClass(vDom.root.childNodes[0].childNodes[i], 'hidden')
        }
    }
}
</code>
