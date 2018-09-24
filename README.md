
#1. 表头设计
原理：
和多叉树的原理类似，参考了它的展示形式。
![多叉树.png](https://upload-images.jianshu.io/upload_images/12890819-f4bcf1a0b1e45112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


表头说明：
如果没有孩子节点就只返回如下一个字段：
- name ：名字

如果有孩子节点，就把数据加在children里面，层层嵌套，返回字段如下： 

- name ：名字
- children : 孩子节点


数据结构格式，参考如下代码：
```
headerData:[
            {
                name: '地区',
            },
            {
                name: '总数据',
                children: [
                    {
                        name: '数据1',
                        children: [
                            {
                                name: '数据11',
                                children: [
	                                {
	                                    name: '数据111',
	                                },
	                                {
	                                    name: '数据112',
	                                }
                                ]
                            },
                            {
                                name: '数据12',
                                children: [
	                                {
	                                    name: '数据121',
	                                },
	                                {
	                                    name: '数据122',
	                                }
                                ]
                            },
                            {
                                name: '数据13',
                                children: [
	                                {
	                                    name: '数据131',
	                                },
	                                {
	                                    name: '数据132',
	                                }
                                ]
                            },
                            {
                                name: '数据14',
                            },

                        ]
                    }
                ]
            }
        ];
```
**表头的宽高方面，前端计算，后端不用管，按照如下格式返回数据即可。**

#2. 表格数据格式
每一项按照表头展示的顺序返回，通过数组的形式
返回一个参数：
- bodyData：总数据

数据结构格式参考代码如下：
```
bodyData:[
        ["地区最先","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"],
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"], 
        ["地区","数据111","数据112","数据121","数据122","数据131","数据132","数据14"], 
        ["地区最后","数据111","数据112","数据121","数据122","数据131","数据132","数据14"], 
    ]
```
#3. 效果
如上表头与表格数据代码生成的效果如图：
![效果.png](https://upload-images.jianshu.io/upload_images/12890819-d0d66ae4198dc89a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 4. 代码
语法高亮用到 codemirror 插件

```
/**
 * 递归遍历 格式化数组
 * @param { Array } paramArr 目标数组
 * @param { Number } level 层级
 */
export function formatArray(paramArr, level) {
  let levelFirst = Number(level)
  const arr = []
  let childArr = []
  for (let i = 0; i < paramArr.length; i++) {
      let obj = {}
      for (let j in paramArr[i]) {
          if (j != 'children') {
              obj[j] = paramArr[i][j]
          }
          obj['level'] = levelFirst
          obj['width'] = getLeafCountTree(paramArr[i])
          if (!paramArr[i].children) {
              obj['childrenNumber'] = 0
              // LeafNode: 叶子节点就是树中最底段的节点
              // obj['isLeafNode'] = true
          } else {
              // obj['isLeafNode'] = false
              obj['childrenNumber'] = paramArr[i].children.length
          }
      }
      arr.push(obj)
      if (paramArr[i].children) {
          let lev = Number(levelFirst) + 1
          childArr = childArr.concat(formatArray(paramArr[i].children, lev));
      }
  }
  let endArr = arr.concat(childArr)
  return endArr
}
```
```
/**
 * 获取 节点的所有叶子节点个数
 * @param {Object} json Object对象
 */
export function getLeafCountTree(json) {
  if(!json.children){
      return 1;
  }else{
      var leafCount = 0;
      for(var i = 0 ; i < json.children.length ; i++){
          leafCount = leafCount + getLeafCountTree(json.children[i]);
      }
      return leafCount;
  }
}
```
```

// json对对象字符串的格式化，美化
export function  jsonFromat (text_value){
  　　if(text_value == ""){
     　　alert("不能为空");  
     　　return false;
  　　} else {
          var json=eval('(' + text_value + ')');
          text_value=JSON.stringify(json);
          var res="";
          for(var i=0,j=0,k=0,ii,ele;i<text_value.length;i++)
          {//k:缩进，j:""个数
              ele=text_value.charAt(i);
              if(j%2==0&&ele=="}")
              {
                  k--;                
                  for(ii=0;ii<k;ii++) ele="    "+ele;
                  ele="\n"+ele;
              }
              else if(j%2==0&&ele=="{")
              {
                  ele+="\n";
                  k++;     
                  for(ii=0;ii<k;ii++) ele+="    ";
              }
              else if(j%2==0&&ele==",")
              {
                  ele+="\n";
                  for(ii=0;ii<k;ii++) ele+="    ";
              }
              else if(ele=="\"") j++;
              res+=ele;        
          }
          return res
  　　}
  }
```

```

<template>
    <div class="pages-tables " id="pages-tables">
        <div class="textarea">
            <h1 class="title">复杂表头 json 数据格式验证：</h1>
            <p class="message">表头展示效果如下：</p>
            <div class="rolling-table auto-table" ref="tableBox" :style="{height: maxHeight + 'px'}">
                <table class="table" id="table" cellpadding="0" cellspacing="0" ref="rollingTable">
                    <thead ref="thead">
                        <tr v-for="(x,i) in headerList" :key="i">
                            <th class="rows " :class="{'cross': index == 0 && i == 0}" v-for="(l,index) in x" :key="index" :colspan="l.width" :rowspan="l.height">{{l.name}}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(b,i) in bodyList" :key="i + 'a'">
                            <template v-for="(x, index) in b">
                                <td :class="{'cols':  index == 0 }" :key="index + 'b'">
                                    {{ x | valueFromt }}
                                </td>
                            </template>
                        </tr>
                        <tr></tr>
                    </tbody>
                </table>
            </div>
            <p class="message">提示：输入 json 覆盖原来的即可，且有验证 json 格式是否正确的功能</p>
            <div class="control">
                <!-- <div class="content fl">
                    <p>select a theme:
                        <select @change="selectTheme" v-model="selected">
                            <option>default</option>
                            <option>night</option>
                            <option>monokai</option>
                            <option>neat</option>
                            <option>elegant</option>
                            <option>cobalt</option>
                            <option>eclipse</option>
                            <option>rubyblue</option>
                            <option>lesser-dark</option>
                            <option>xq-dark</option>
                        </select>
                    </p>
                </div>
                <div class="content fl ml20">
                    <p>select the editor language:
                        <select @change="selectMode" v-model="mode">
                            <option>javascript</option>
                            <option>php</option>
                            <option>python</option>
                            <option>vue</option>
                            <option>xml</option>
                            <option>sql</option>
                            <option>http</option>
                            <option>css</option>
                            <option>sass</option>
                            <option>jsx</option>
                            <option>django</option>
                        </select>
                    </p>
                </div>
                <div class="content fl ml20">
                    <p>select keyMap:
                        <select @change="selectKeyMap" v-model="keyMap">
                            <option>default</option>
                            <option>emacs</option>
                            <option>sublime</option>
                            <option>vim</option>
                        </select>
                    </p>
                </div> -->
                <div class="fl ml20 mt20 mb10 submit">
                    <mt-button type="primary"  size="small" @click.native="setInputValue">提交</mt-button>
                </div>
                <div class="clearfix"></div>
            </div>
            <textarea id="code" name="code">
                [
                    {
                        name: '地区',
                    },
                    {
                        name: '总数据',
                        children: [
                            {
                                name: '数据1',
                                children: [
                                    {
                                        name: '数据11',
                                        children: [{
                                            name: '数据111',
                                        },
                                        {
                                            name: '数据112',
                                        }
                                        ]
                                    },
                                    {
                                        name: '数据12',
                                        children: [{
                                            name: '数据121',
                                        },
                                        {
                                            name: '数据122',
                                        }
                                        ]
                                    },
                                    {
                                        name: '数据13',
                                        children: [{
                                            name: '数据131',
                                        },
                                        {
                                            name: '数据132',
                                        }
                                        ]
                                    },
                                    {
                                        name: '数据14',
                                    },
                                    {
                                        name: '数据15',
                                    },
                                    {
                                        name: '数据16数据16数据16数据16',
                                    },
                                    {
                                        name: '数据17',
                                    },
                                ]
                            }
                        ]
                    }
                ];
            </textarea>
        </div>

    </div>
</template>
<script>

// 说明这个 demo 是给 pc 端用的，单位要为 px
import { formatArray, getLeafCountTree, jsonFromat } from "libs/common/common";
import { Button, MessageBox } from 'mint-ui';
import * as CodeMirror from 'codemirror/lib/codemirror'
// 根据设置的主题，引入相应的主题包,主题包存储在theme下，使用其他主题包时设置option中theme为对应主题
import 'codemirror/lib/codemirror.css'
import 'codemirror/theme/monokai.css'
import 'codemirror/theme/neat.css'
import 'codemirror/theme/elegant.css'
import 'codemirror/theme/night.css'
import 'codemirror/theme/cobalt.css'
import 'codemirror/theme/eclipse.css'
import 'codemirror/theme/rubyblue.css'
import 'codemirror/theme/xq-dark.css'
// styleActiveLine: 设置光标所在行高亮true/false，需引入工具包：
import 'codemirror/addon/selection/active-line'
// 根据设置的编辑器语言，引入相应工具包,以下为常用语言包
import 'codemirror/mode/javascript/javascript'
import 'codemirror/mode/go/go'
import 'codemirror/mode/php/php'
import 'codemirror/mode/python/python'
import 'codemirror/mode/http/http'
import 'codemirror/mode/sql/sql'
import 'codemirror/mode/vue/vue'
import 'codemirror/mode/xml/xml'
import 'codemirror/mode/css/css'
import 'codemirror/mode/sass/sass'
import 'codemirror/mode/jsx/jsx'
import 'codemirror/mode/django/django'
// keyMap：快捷键，default使用默认快捷键，除此之外包括emacs，sublime，vim快捷键，使用需引入工具
import 'codemirror/keymap/sublime.js'
import 'codemirror/keymap/emacs.js'
import 'codemirror/keymap/vim.js'
// extraKeys 快捷键,例如 {“Ctrl-Q”: “autocomplete”}：自动补全使用需要引入工具
import 'codemirror/addon/hint/show-hint'
import 'codemirror/addon/hint/javascript-hint'
import 'codemirror/addon/hint/sql-hint'
import 'codemirror/addon/hint/html-hint'
import 'codemirror/addon/hint/xml-hint'
import 'codemirror/addon/hint/anyword-hint'
import 'codemirror/addon/hint/css-hint'
import 'codemirror/addon/hint/show-hint'

export default {
    data() {
        return {
            mapArray: [],
            keyMap: 'default',
            mode: 'javascript',
            editor: '',
            selected: 'monokai',
            header: '',
            maxHeight: '100%',
            theadHeight: '100%',
            offsetHeight: 0,
            scroll: {
                scroller: null
            },
            headerList: [],
            bodyList: [],

        }
    },
    filters: {
        valueFromt: function (value) {
            let realValue = ''
            if (!value) return ''
            value = value.toString()
            if (value.length > 20) {
                realValue = value.slice(0, 15) + '...'
            } else {
                realValue = value
            }
            return realValue
        },
    },
    methods: {
        selectKeyMap(){
            this.editor.addKeyMap(this.keyMap)  
        },
        selectMode(){
            this.editor.setOption("mode",this.mode)   
        },
        selectTheme() {
            this.editor.setOption("theme", this.selected);
        },
        setInputValue() {
            this.header = this.editor.getValue();
            if(this.header){
                this.change()
            }
        },
        change() {
            try {
                const newData = formatArray(eval(this.header), 0)
                let maxLevel = newData[newData.length - 1].level
                this.setHeight(newData, maxLevel + 1)
                this.arayLayered(newData, maxLevel)
                this.headerList = this.arayLayered(newData, maxLevel)
            } catch (e) {
                console.log('e:', e)
                MessageBox('提示', '请检查 json 格式是否正确！！!');
            }
        },
        setHeight(arr, maxLevel) {
            // console.log("setHeight maxLevel", maxLevel)
            for (let i = maxLevel; i >= 0; i--) {
                for (let j = 0; j < arr.length; j++) {
                    // 设置高
                    if (arr[j].childrenNumber) {
                        arr[j].height = 1
                    } else {
                        arr[j].height = maxLevel - arr[j].level
                    }
                }
            }
            return arr
        },
        arayLayered(arr, maxLevel) {
            let returnArr = []
            for (let i = 0; i <= maxLevel; i++) {
                let arrLevel = []
                for (let j = 0; j < arr.length; j++) {
                    if (arr[j].level == i) {
                        arrLevel.push(arr[j])
                    }
                }
                returnArr[i] = arrLevel
            }
            return returnArr
        }
    },
    mounted() {
        let bodyListA = [
            ["地区最先", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
            ["地区最后", "数据111", "数据112", "数据121", "数据122", "数据131", "数据132", "数据14"],
        ]

        const data = [
            {
                name: '地区',
            },
            {
                name: '总数据',
                children: [
                    {
                        name: '数据1',
                        children: [
                            {
                                name: '数据11',
                                children: [{
                                    name: '数据111',
                                },
                                {
                                    name: '数据112',
                                }
                                ]
                            },
                            {
                                name: '数据12',
                                children: [{
                                    name: '数据121',
                                },
                                {
                                    name: '数据122',
                                }
                                ]
                            },
                            {
                                name: '数据13',
                                children: [{
                                    name: '数据131',
                                },
                                {
                                    name: '数据132',
                                }
                                ]
                            },
                            {
                                name: '数据14',
                            },
                            {
                                name: '数据15',
                            },
                            {
                                name: '数据16数据16数据16数据16',
                            },
                            {
                                name: '数据17',
                            },
                        ]
                    }
                ]
            }
        ];
        this.header = jsonFromat(JSON.stringify(data))
        const newData = formatArray(data, 0)
        let maxLevel = newData[newData.length - 1].level
        this.setHeight(newData, maxLevel + 1)
        this.arayLayered(newData, maxLevel)
        this.headerList = this.arayLayered(newData, maxLevel)

        this.editor = CodeMirror.fromTextArea(document.getElementById("code"), {
            // value : data,  // 文本域默认显示的文本
            lineNumbers: true, /* 定义是否显示行号 */
            mode: "javascript",  /* 定义语法的类型，如果是html则为：text/html */
            theme: "monokai", /* 定义主题 */
            smartIndent: true,  // 是否智能缩进
            styleActiveLine: true,
            keymap:"defaule"

        });
        // this.editor.on("changes",() =>{
        //     //编译器内容更改事件
        //     this.setInputValue();
        // });
        this.editor.setSize(1200,500)
    }
}

</script>
<style lang="less" >
.CodeMirror {
    border: 1px solid black;
    line-height: 16px;
    font-size: 16px;
    text-align: left;
}
</style>
<style lang="less" scoped>
.submit{
    margin-left: 580px;
    margin-top: 20px;
}
textarea{
	width: 1300px;
	height: 1300px;
}
.content{
    font-size: 16px;
}
.textarea{
    text-align: center;
    font-size: 20px;
    .title{
        margin-top: 20px;
        font-size: 30px;
        color: #333;
    }
    textarea{
        border: 1px solid #eee;
        font-size: 16px;
        resize: both;
        width: 800px;
        min-height: 900px;
    }
}
.message{
    color: red;
    font-size: 16px;
}
.waterMask {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: 4;
    pointer-events: none;
}
.pages-tables {
  -webkit-overflow-scrolling: touch; // ios滑动顺畅
  position: relative;
  margin-left: 5%;
  padding-bottom: 160px;
  margin: 0 auto;
  width: 1200px;
}
.rolling-table {
    height: 100%;
    font-size: 0.28rem;
    color: #86939a;
    background-color: #fff;
    width: 100%;
    -webkit-overflow-scrolling: touch;
    position: relative;
    top: 0;
    // overflow: hidden;
  }
.rows {
    position: relative;
    z-index: 3;
}
.cross {
    position: relative;
    z-index: 5;
}
table td {
  border: 0px solid #000;
  font-size: 25px;
  background: #fff;
}
::-webkit-scrollbar {
    display: none;
}
.table {
  border-collapse: collapse; //去掉重复的border
  color: #86939e;
  font-size: 25px;
  border: 0px solid #000;
  min-height: 100%;
  text-align: center;
  td {
    border-bottom: 1px solid #eee;
    height: 30px;
    line-height: 30px;
    padding: 0 0.2rem;
    // white-space: nowrap;
    white-space: inherit;
    max-width: 500px;
    min-width: 50px;
    // overflow:hidden; 
    // text-overflow:ellipsis;
    // -webkit-line-clamp:2; 
  }
  th {
    color: #43484d;
    white-space: pre-wrap;
    height: 36px;
    line-height: 36px;
    padding: 5px 6px;
    background-color: #f3f4f6;
    font-weight: normal;
    padding-bottom: 0;
    padding-top: 0;
    max-width: 200px;
    border: 1px solid red;
    &:last-child{
        // border-right: 0rem solid #e4e8f5;
    }
  }
}
tr{
    position: relative;
    background-color: #fff;
    &:nth-of-type(odd){
        td{
            background-color: #ebf9fc;
        }
    }
}
</style>

```

# 5. 效果链接：
效果链接如下:

[复杂表格设计数据格式](https://biaochenxuying.github.io/reg-json/index.html#/tableDemo)

动态效果：
![动态效果.gif](https://upload-images.jianshu.io/upload_images/12890819-f7a971de60d4c291.gif?imageMogr2/auto-orient/strip)
