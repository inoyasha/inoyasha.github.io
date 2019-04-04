---
title: ueditor1.3.6 扩展插件(一键排版)
tags:
  - ueditor
categories:
  - 前端
author: 郭小黑
translate_title: ueditor-1.3.6-extension-(one-key-typesetting)
date: 2019-01-23 17:51:00
---

### udeitor 百度编辑器

现在的ueditor 1.4.3版本可以直接新建文件夹在外部编写相应的插件来实现对百度编辑器的扩展。但是在ueditor1.3.6版本上没有看到有提供这样的扩展。所以在编辑器的内部编写自定义按钮功能。并实现自己的过滤规则。

<!-- more -->


- 找到ueditor.all.js文件中找到 

  btnCmds 数组中添加 typesetting
```javascript
//为工具栏添加按钮，以下都是统一的按钮触发命令，所以写在一起
    var btnCmds = ['undo', 'redo', 'formatmatch',
        'bold', 'italic', 'underline', 'fontborder', 'touppercase', 'tolowercase',
        'strikethrough', 'subscript', 'superscript', 'source', 'indent', 'outdent',
        'blockquote', 'pasteplain', 'pagebreak',
        'selectall', 'print','horizontal', 'removeformat', 'time', 'date', 'unlink',
        'insertparagraphbeforetable', 'insertrow', 'insertcol', 'mergeright', 'mergedown', 'deleterow',
        'deletecol', 'splittorows', 'splittocols', 'splittocells', 'mergecells', 'deletetable', 'drafts','typesetting'];
```

- 打开ueditor.config.js中找到 toolbars数组，在toolsbars数组中添加 'typesetting'

```javascript
toolbars:[
			['fullscreen', 'source', '|', 'undo', 'redo', '|',
				'bold', 'italic', 'underline',  'strikethrough', 'superscript', 'subscript', 'removeformat', 'formatmatch', 'autotypeset', 'blockquote', 'pasteplain', '|', 'forecolor', 'backcolor', 'insertorderedlist', 'insertunorderedlist', 'selectall', 'cleardoc', '|',
				'rowspacingtop', 'rowspacingbottom', 'lineheight', '|',
				'customstyle', 'paragraph', 'fontfamily', 'fontsize', '|',
				 'indent', '|',
				'justifyleft', 'justifycenter', 'justifyright', 'justifyjustify', '|', '|',
				'link', 'unlink', 'anchor', '|',
				'insertimage', 'emotion', /*'insertvideo',*/  'attachment', 'map', /*'gmap',*/ 'insertframe','pagebreak',   '|',
				'spechars', /*'snapscreen',*/ 'wordimage', '|',
				'inserttable', 'deletetable', 'insertparagraphbeforetable', 'insertrow', 'deleterow', 'insertcol', 'deletecol', 'mergecells', 'mergeright', 'mergedown', 'splittocells', 'splittorows', 'splittocols', 'charts', '|',
				'print', 'preview', 'searchreplace', 'drafts','typesetting']
          ]
```
- 在ueditor.css中添加插件的样式

```css
.edui-for-typesetting .edui-icon {
	/*自定义命令按钮的样式*/
	background-position: -236px -76px;
}
```

- 打开zh-cn.js 找到UE.I18N['zh-cn'] 在数字中添加 'typesetting':'一键排版'

```javascript
UE.I18N['zh-cn'] = {
    'labelMap':{
        'anchor':'锚点', 'undo':'撤销', 'redo':'重做', 'bold':'加粗', 'indent':'首行缩进', 'snapscreen':'截图',
        'italic':'斜体', 'underline':'下划线', 'strikethrough':'删除线', 'subscript':'下标','fontborder':'字符边框',
        'superscript':'上标', 'formatmatch':'格式刷', 'source':'源代码', 'blockquote':'引用',
        'pasteplain':'纯文本粘贴模式', 'selectall':'全选', 'print':'打印', 'preview':'预览',
        'horizontal':'分隔线', 'removeformat':'清除格式', 'time':'时间', 'date':'日期',
        'unlink':'取消链接', 'insertrow':'前插入行', 'insertcol':'前插入列', 'mergeright':'右合并单元格', 'mergedown':'下合并单元格',
        'deleterow':'删除行', 'deletecol':'删除列', 'splittorows':'拆分成行', 'splittocols':'拆分成列', 'splittocells':'完全拆分单元格',
        'mergecells':'合并多个单元格', 'deletetable':'删除表格', 'cleardoc':'清空文档','insertparagraphbeforetable':"表格前插入行",'insertcode':'代码语言','fontfamily':'字体', 'fontsize':'字号', 'paragraph':'段落格式', 'insertimage':'图片',
        'edittable':'表格属性','edittd':'单元格属性', 'link':'超链接','emotion':'表情', 'spechars':'特殊字符', 'searchreplace':'查询替换', 'map':'Baidu地图', 'gmap':'Google地图',
        'insertvideo':'视频', 'help':'帮助', 'justifyleft':'居左对齐', 'justifyright':'居右对齐', 'justifycenter':'居中对齐',
        'justifyjustify':'两端对齐', 'forecolor':'字体颜色', 'backcolor':'背景色', 'insertorderedlist':'有序列表',
        'insertunorderedlist':'无序列表', 'fullscreen':'全屏', 'directionalityltr':'从左向右输入', 'directionalityrtl':'从右向左输入',
        'rowspacingtop':'段前距', 'rowspacingbottom':'段后距',  'pagebreak':'分页', 'insertframe':'插入Iframe', 'imagenone':'默认',
        'imageleft':'左浮动', 'imageright':'右浮动', 'attachment':'附件', 'imagecenter':'居中', 'wordimage':'图片转存',
        'lineheight':'行间距','edittip' :'编辑提示','customstyle':'自定义标题', 'autotypeset':'自动排版', 'webapp':'百度应用',
        'touppercase':'字母大写', 'tolowercase':'字母小写','background':'背景','template':'模板','scrawl':'涂鸦','music':'音乐',inserttable:'插入表格',
        'drafts': '从草稿箱加载', 'charts': '图表','typesetting':'一键排版'
    },
```

- 打开ueditor.all.js 编写自定义插件内容

```javascript
/**
     * 一键排版 自定义插件
     */
    UE.plugins['typesetting'] = function (){
        var me = this;
        //状态 是否进行一键排版操作  0 代表没有进行一键排版
        var flag = 0;
        //用来保存一键排版前的html字符串
        var temp = "";
        //要在这里编写过滤规则 filterTypeSettingRules 参考了纯文本模式
        me.setOpt({
            'filterTypeSettingRules' : function(){

                /**
                 * 根据获取到的value 设置节点的属性
                 * @param node  节点
                 * @param key 秘钥
                 * @param value 属性值
                 */
                function setAttrValue(node,key,value){
                    node.setAttr(key,value);
                }

                /**
                 * 清除自带样式 不知道ueditor 自带的 UE.dom.domUtils 的静态方法 调用方法不好使
                 * 所以采用将 制定的样式 style 设置成空的形式  暴力解决问题
                 * @param node
                 */
                function removeAttr(node) {
                    //因为要重置 style 后面还需要获取style属性 所以暂时 不清楚style属性
                    /*if(node.getAttr("style")){
                        node.setAttr("style","");
                    }*/
                    if(node.getAttr("class")){
                        node.setAttr("class","");
                    }
                    if(node.getAttr("className")){
                        node.setAttr("className","");
                    }
                    if(node.getAttr("id")){
                        node.setAttr("id","");
                    }
                    if(node.getAttr("name")){
                        node.setAttr("name","");
                    }
                    if(node.getAttr("width")){
                        node.setAttr("width","");
                    }
                }
                /**
                 * 根据属性名获取节点值
                 * @param node
                 * @param key
                 * @param value
                 */
                function getNodeAttr(node,key,value){
                    //如果节点存在
                    if(node){
                        return node.getAttr(key,value);
                    }
                    return null;
                }

                /**
                 * 获取 node节点的纯文本
                 * @param node
                 */
                function getNodeText (node){
                    var text = node.innerText();
                    return text;
                }

                /**
                 * 根据传入的节点来处理包含的span元素  包括循环嵌套
                 * @param node
                 */
                function eachChildNode(node){
                        UE.utils.each(node.getNodesByTagName('span'),function(tNode) {
                            //TODO
                            var name = tNode.tagName;
                            switch (name){
                                case "span":
                                    removeNode(tNode)
                                    break;
                            }

                        });
                }

                /**
                 * 判断居中问题 给true|flase 返回值
                 * 返回 true|false 通过node的center属性或者是style的text-align:属性
                 * @param node
                 */
                function isCenter(node){
                    //UE.dom.domUtils.getStyle( testNode, "color" )  这个不知道好不好用 所以采用一种特别的方式来处理
                    //默认为不居中
                    var align = false;
                    var temp = node.getAttr("style");
                    var arg=0;
                    if(typeof(temp)!="undefined"&&temp!=null){
                        arg = node.getAttr("style").toLowerCase().indexOf("center");
                    }
                    if(node.getAttr('align')=='center' || arg>0){
                        align = true;
                    }
                    return align;
                }
                function transP(node){
                    node.tagName = 'p';
                    node.setStyle();
                }

                /**
                 * 处理标题公共部分
                 * @param node
                 */
                function handleTitle(node){
                    var text = getNodeText(node);
                    removeAttr(node);
                    //h2 嵌套span元素处理
                    eachChildNode(node);
                    node.innerText(text);
                }
                /**
                 * 处理 h1
                 * @param node
                 */
                function addH1Style(node) {
                    handleTitle(node);
                    //先清除自带的样式，然后在为每一个节点添加样式
                    node.setAttr("style","font-size: 30px;font-family: 仿宋;color: #4c3339;margin:10px 0 10px 0;font-weight: bold;");
                }
                /**
                 * 处理 h2
                 * @param node
                 */
                function addH2Style(node) {
                    handleTitle(node);
                    node.setAttr("style","font-size: 26px;font-family: 仿宋;color: #4c3339;margin:10px 0 10px 0;font-weight: bold;");
                }
                /**
                 * 处理 h3
                 * @param node
                 */
                function addH3Style(node) {
                    handleTitle(node);
                    node.setAttr("style","font-size: 22px;font-family: 仿宋;color: #4c3339;margin:10px 0 10px 0;font-weight: bold;");
                }
                /**
                 * 处理 h4
                 * @param node
                 */
                function addH4Style(node){
                    handleTitle(node);
                    node.setAttr("style","font-size: 18px;font-family: 仿宋;color: #4c3339;margin:10px 0 10px 0;font-weight: bold;");
                }
                /**
                 * 处理 h5
                 * @param node
                 */
                function addH5Style(node) {
                    handleTitle(node);
                    node.setAttr("style","font-size: 14px;font-family: 仿宋;color: #4c3339;margin:10px 0 10px 0;font-weight: bold;");
                }
                /**
                 * 处理 h6
                 * @param node
                 */
                function addH6Style(node) {
                    handleTitle(node);
                    node.setAttr("style","font-size: 12px;font-family: 仿宋;color: #4c3339;margin:10px 0 10px 0;font-weight: bold;");
                }

                /**
                 * 处理表格
                 * @param node
                 */
                function addTableStyle(node){
                        //可以将浏览器中相同的属性获取一样的 TODO
                        var cellspacing = node.getAttr("cellspacing");
                        var cellpadding = node.getAttr("cellpadding");
                        var align = node.getAttr("align");
                        var style = node.getAttr("style");
                        //将获取到的table属性设置到格式化的表格中。

                       //为了防止 table表格过宽导致跨出页面宽度 为table 添加一个最大宽度

                        var otherStyle = "margin:10px 0 10px 0";
                        setAttrValue(node,"cellspacing",cellspacing);
                        setAttrValue(node,"cellpadding",cellpadding);
                        setAttrValue(node,"align",align);
                        setAttrValue(node,"style",style + otherStyle);
                }

                /**
                 * 处理td
                 * @param node
                 */
                function addTdStyle(node) {
                    //保留td的样式
                    var width = node.getAttr("width");
                    var colspan = node.getAttr("colspan");
                    var style = node.getAttr("style");

                    setAttrValue(node, "width", width);
                    setAttrValue(node, "colspan", colspan);
                    setAttrValue(node, "style", style);

                    //td属性不变但是里面的文字有变化
                    //遍历td节点集合  p和span被覆盖掉了 暂时没有什么好的办法
                    //处理td下面的节点 怎么做 这里的样式才不会被其他的样式覆盖掉？？
                    UE.utils.each(node.getNodesByTagName("p span"), function (tNode) {
                        var tagName = tNode.tagName;
                        switch (tagName) {
                            case "p":
                                //对p本身的处理
                                var pStyle = tNode.getAttr("style");
                                //这个地方进行 强制处理 如果sytle样式已经 被覆盖掉了。先获取 本身的样式
                                removeAttr(node);
                                if(typeof(pStyle)!="undefined"&&pStyle!=null){
                                    setAttrValue(tNode, "style", pStyle);
                                }
                                setAttrValue(tNode,"name","tdChild");

                                /*//p下的文字有嵌套  没啥卵用
                                 UE.utils.each(tNode.getNodesByTagName('span'),function(spanNode){
                                    //判断 是否存在
                                    if(spanNode.tagName=="span"){
                                        var spanStyle = spanNode.getAttr("style");
                                        removeAttr(node);
                                        if(typeof(spanStyle)!="undefined"&&spanStyle!=null){
                                            setAttrValue(spanNode, "style", spanStyle);
                                        }
                                        setAttrValue(spanNode,"name","tdChild");
                                    }
                                 });
                                break;*/
                            case "span":
                                var spanStyle = tNode.getAttr("style");
                                removeAttr(node);
                                if(typeof(spanStyle)!="undefined"&&spanStyle!=null){
                                    setAttrValue(tNode, "style", spanStyle);
                                }
                                setAttrValue(tNode,"name","tdChild");
                                break;
                            }
                    });
                }

                /**
                 * 处理tr
                 * @param node
                 */
                function addTrStyle(node){
                    var trStyle = node.getStyle("style");
                    setAttrValue(node,"style",trStyle);
                }

                /**
                 * 处理p
                 * @param node
                 */
                function addPStyle(node){
                    var name = node.getAttr("name");
                    removeAttr(node);
                    //本身样式暂时不知道怎么处理 暂时先不处理
                    var addStyle = "font-size: 21px;font-family: 仿宋;color: #4c3339;text-indent: 2em;line-height: 28px;";
                    if(typeof(name)!="undefined"&&name!=null) {
                        if (name == "tdChild"){
                            return ;
                        }
                    }
                    //如果子标签有img的话 为了给img 居中 设置样式为居中
                    UE.utils.each(node.getNodesByTagName('img'),function(tNode) {
                        var name = tNode.tagName;
                        switch (name){
                            case "img":
                                addStyle = addStyle + "text-align:center";
                                break;
                        }

                    });
                    if (isCenter(node)){
                        addStyle = addStyle + "text-align:center";
                    }
                    node.setAttr("style",addStyle)
                    node.setAttr("name","singleP");
                }

                /**
                 * 处理a
                 * @param node
                 */
                function addAStyle(node){
                    removeAttr(node);
                    node.setAttr("style","font-size: 21px;font-family: 仿宋;color: #5779ba;line-height: 28px;text-decoration:none;");
                }

                /**
                 * 处理 span
                 * @param node
                 */
                function addSpanStyle(node){
                    var name = node.getAttr("name");
                    removeAttr(node);
                    //td 下的p 和span 的冲突处理
                    if(typeof(name)!="undefined"&&name!=null) {
                        if (name = "tdChild"){
                            return ;
                        }
                    }
                    node.setAttr("style","font-size: 21px;font-family: 仿宋;color: #4c3339;text-indent: 2em;line-height: 28px;");
                    node.setAttr("name","singSpan");
                }

                /**
                 * 对img进行处理
                 * @param node
                 */
                function addImgStyle(node){
                    //处理转存word图片的情况 if是word转存 需要处理图片 如果没有将图片上传到服务器 则需要删除元素图片元素节点
                    //一键排版 对于图片的描述 如果没有上传到服务器上面 那么 对于图片的描述没有办法进行处理，因为这个时候没办法判断 下面的节点是作为图片描述还是 正常的段落
                    var word_img = node.getAttr("word_img");
                    if(typeof(word_img)!="undefined"&&word_img!=null){
                        //清除节点 并且 返回 不做下面的操作
                        removeNode(node);
                        return ;
                    }
                    var src = node.getAttr("src");
                    //写死图片样式 没有考虑图片压缩的情况 因为取不到图片的质量
                    var imgStyle = "text-align:center;max-width:500px;padding:0 200px 0 200px;"
                    node.setAttr("src",src);
                    node.setAttr("style",imgStyle);
                }

                function removeNode(node){
                    node.parentNode.removeChild(node,true)
                }


                return {
                    //直接删除及其字节点内容
                    //'-' : 'script style object iframe embed input select',
                    'p': addPStyle,
                    'br':{$:{}},
                    div: function (node) {
                        alert(node.tagName);
                        var tmpNode, p = UE.uNode.createElement('p');
                        while (tmpNode = node.firstChild()) {
                            if (tmpNode.type == 'text' || !UE.dom.dtd.$block[tmpNode.tagName]) {
                                p.appendChild(tmpNode);
                            } else {
                                if (p.firstChild()) {
                                    node.parentNode.insertBefore(p, node);
                                    p = UE.uNode.createElement('p');
                                } else {
                                    node.parentNode.insertBefore(tmpNode, node);
                                }
                            }
                        }
                        if (p.firstChild()) {
                            node.parentNode.insertBefore(p, node);
                        }
                        node.parentNode.removeChild(node);
                    },
                    ol: removeNode,
                    ul: removeNode,
                    dl:removeNode,
                    dt:removeNode,
                    dd:removeNode,
                    'li':removeNode,
                    'a':addAStyle,
                    'table':addTableStyle,
                    'tr':addTrStyle,
                    'td':addTdStyle,
                    'span':addSpanStyle,
                    //'caption':transP,
                    //'th':transP,
                    //'tr':transP,
                    'h1':addH1Style,'h2':addH2Style,'h3':addH3Style,'h4':addH4Style,'h5':addH5Style,'h6':addH6Style,
                    'img':addImgStyle
                    //不清除 表格td的样式
                    /*'td':function(node){
                        //没有内容的td直接删掉
                        var txt = !!node.innerText();
                        if(txt){
                            node.parentNode.insertAfter(UE.uNode.createText(' &nbsp; &nbsp;'),node);
                        }
                        node.parentNode.removeChild(node,node.innerText())
                    }*/
                }
            }()
        });

        me.commands['typesetting'] = {
            // 暂时不做扩展，不通过传递参数指定排版的位置，选取所有的选区，对所有的内容进行一键排版
            execCommand : function(cmdName,style){
                //根据编辑器的内容获取uNode节点 调用UE的静态方法 htmlparser
                var root = UE.htmlparser(me.getContent(),true);
                //如果内容为空则返回，不进行一键排版
                var html = "";
                //先将未过滤的字符串存到一个变量里面
                var meHtml = me.getContent();
               if(me.getContent()==""){
                   return ;
               }
                if(flag==0){
                    temp = meHtml;
                    html =  UE.filterNode(root, me.options.filterTypeSettingRules).toHtml();
                    flag = 1;
                }else{
                    //恢复到一键排版前的状态
                    html = temp;
                    flag = 0;
                    //将temp清空 手动清理占用
                    temp = "";
                }
                me.setContent(html);
            },
            queryCommandState:function(){
                return flag == 1?1:0;
            }
        };
    };
```

