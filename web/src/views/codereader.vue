<template>
  <div class="dashboard-container">
    <div class="dashboard-editor-container" style="">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '#' }">代码阅读</el-breadcrumb-item>
        <el-breadcrumb-item style="font-size: 20px;line-height:0.5;">程序流程图</el-breadcrumb-item>
      </el-breadcrumb>
      <el-row  style="padding-bottom:0">
        <div class="question-intr mt2 mb2">
          <div class="subject-item-wrap" align="left">
            <h3>函数调用：自动生成函数调用图</h3>
            <h3>代码结构：自动生成基于AST的代码结构流程图</h3>
            <h3>程序流程图：自动生成基于注释的程序流程图</h3>
            <li>代码注释：自动生成代码注释</li>
            <li>代码摘要：基于深度学习的代码摘要生成</li>
          </div>
        </div>
      </el-row>
      <el-tabs type="border-card" v-model="graphcards" @tab-click="handleClick">
        <el-tab-pane label="函数调用" name="callgraph">
          <h3 class="subject-item-title" align="left">函数调用</h3>
          <el-row :gutter="20">
            <el-col :span="12">
              <div class="grid-content topic_window" >
                <div align="left">
                  <img :src="callGraph_img" class="image">
                </div>
              </div>
            </el-col>
            <el-col :span="12" align="left">
              <div class="grid-content topic_window">
                <div class="code-mirror-div">
                  <div class="tool-bar" style="">
                    <span style="color: whitesmoke;font-size:14px">编程语言</span>
                    <el-select
                      v-model="cmEditorMode"
                      size="mini"
                      style="width:100px"
                      @change="onEditorModeChange">
                      <el-option
                        v-for="item in cmEditorModeOptions"
                        :key="item"
                        :label="item"
                        :value="item"
                      ></el-option>
                    </el-select>
                  </div>
                  <my_cm  class="code-edit"
                          ref="cmEditor_callGraph"
                          :cmTheme="cmTheme"
                          :cmMode="cmMode"
                          :editorValue="editorValue">
                  </my_cm>
                </div>
                <el-button type="primary" style="padding: 10px;"   @click="submitCode('callGraph')">提交代码生成函数调用图</el-button>
              </div>
            </el-col>

          </el-row>
        </el-tab-pane>
        <el-tab-pane label="代码结构" name="AST">
          <h3 class="subject-item-title" align="left">代码结构</h3>
          <el-row :gutter="20">
            <el-col :span="12">
              <div class="grid-content topic_window" >
                <div align="left">
                  <div id="AST_graph"></div>
                </div>
              </div>
            </el-col>
            <el-col :span="12" align="left">
              <div class="grid-content topic_window">
                <div class="code-mirror-div">
                  <div class="tool-bar" style="">
                    <span style="color: whitesmoke;font-size:14px">编程语言</span>
                    <el-select
                      v-model="cmEditorMode"
                      size="mini"
                      style="width:100px"
                      @change="onEditorModeChange">
                      <el-option
                        v-for="item in cmEditorModeOptions"
                        :key="item"
                        :label="item"
                        :value="item"
                      ></el-option>
                    </el-select>
                  </div>
                  <my_cm  class="code-edit"
                          ref="cmEditor_AST"
                          :cmTheme="cmTheme"
                          :cmMode="cmMode"
                          :editorValue="editorValue">
                  </my_cm>
                </div>
                <el-button type="primary" style="padding: 10px;"   @click="submitCode('AST')">提交代码生成代码结构图</el-button>
              </div>
            </el-col>

          </el-row>
        </el-tab-pane>
        <el-tab-pane label="程序流程图" name="flowchart">
          <h3 class="subject-item-title" align="left">程序流程图</h3>
          <el-row :gutter="20">
            <el-col :span="12">
              <div class="grid-content topic_window" >
                <div align="left">
                  <img :src="FC_img" class="image">
                </div>
              </div>
            </el-col>
            <el-col :span="12" align="left">
              <div class="grid-content topic_window">
                <div class="code-mirror-div">
                  <div class="tool-bar" style="">
                    <span style="color: whitesmoke;font-size:14px">编程语言</span>
                    <el-select
                      v-model="cmEditorMode"
                      size="mini"
                      style="width:100px"
                      @change="onEditorModeChange">
                      <el-option
                        v-for="item in cmEditorModeOptions"
                        :key="item"
                        :label="item"
                        :value="item"
                      ></el-option>
                    </el-select>
                  </div>
                  <my_cm  class="code-edit"
                          ref="cmEditor_FC"
                          :cmTheme="cmTheme"
                          :cmMode="cmMode"
                          :editorValue="sum_code">
                  </my_cm>
                </div>
                <el-button type="primary" style="padding: 10px;"   @click="submitCode('sum_code')">提交代码生成代码注释</el-button>
                <el-button type="primary" style="padding: 10px;"   @click="submitCode('FC')">提交注释代码生成流程图</el-button>
              </div>
            </el-col>

          </el-row>
        </el-tab-pane>
      </el-tabs>



    </div>



  </div>
</template>

<script>

import axios from "axios"

export default {
  name: "codereader",
  data() {
    return {
      graphcards: 'callgraph',
      callGraph_img: "static\\person.jpg",
      AST_img: "static\\person.jpg",
      AST_img_code:
        "st4471442960=>start: start a_pyflow_test\n" +
        "op4471442064=>operation: do something\n" +
        "cond4471501392=>condition: Yes or No?\n" +
        "io4471501648=>inputoutput: output: something...\n" +
        "e4471501904=>end: end a_pyflow_test\n" +
        "sub4471501584=>subroutine: A Subroutine\n" +
        "\n" +
        "st4471442960->op4471442064\n" +
        "op4471442064->cond4471501392\n" +
        "cond4471501392(yes)->io4471501648\n" +
        "io4471501648->e4471501904\n" +
        "cond4471501392(no)->sub4471501584\n" +
        "sub4471501584(right)->op4471442064",
      FC_img: "static\\person.jpg",
      sum_code:
        '# Python is Supported Now\n' +
        '# code here',
      graphCodeData: {
        graphtype: '',
        content: '',
        languageID: 0,
        languageName: ''
      },
      editorValue:  // 默认代码模板
        '# Python is Supported Now\n' +
        '# code here',
      cmTheme: "blackboard", // codeMirror主题
      cmEditorMode: "Python(3.6)", // 编辑模式
      // 编辑模式选项
      cmEditorModeOptions: [
        "Python(3.6)"
      ],
      cmMode: "python", //codeMirror模式
    };
  },
  created() {
  },
  destroyed(){
    clearInterval(this.myInterval)
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    },
    // 切换语言
    onEditorModeChange(value) {
      switch (value) {
        case "Python(3.6)":
          this.cmMode = "python";
          break;
        default:
          this.cmMode = "python";
      }
    },
    //获取内容
    getValue() {
      let content = this.$refs.cmEditor.getValue();
      return content
    },
    ASTcode2graph(astcode){
      var chart;
      chart = flowchart.parse(astcode);
      this.$message(astcode)
      chart.drawSVG('AST_graph', {
        'line-width': 3,
        'maxWidth': 3,//ensures the flowcharts fits within a certian width
        'line-length': 50,
        'text-margin': 10,
        'font-size': 14,
        'font': 'normal',
        'font-family': 'Helvetica',
        'font-weight': 'normal',
        'font-color': 'black',
        'line-color': 'black',
        'element-color': 'black',
        'fill': 'white',
        'yes-text': 'yes',
        'no-text': 'no',
        'arrow-end': 'block',
        'scale': 1,
        'symbols': {
          'start': {
            'font-color': 'red',
            'element-color': 'green',
            'fill': 'yellow'
          },
          'end':{
            'class': 'end-element'
          }
        },
        'flowstate' : {
          'past' : { 'fill' : '#CCCCCC', 'font-size' : 12},
          'current' : {'fill' : 'yellow', 'font-color' : 'red', 'font-weight' : 'bold'},
          'future' : { 'fill' : '#FFFF99'},
          'request' : { 'fill' : 'blue'},
          'invalid': {'fill' : '#444444'},
          'approved' : { 'fill' : '#58C4A3', 'font-size' : 12, 'yes-text' : 'APPROVED', 'no-text' : 'n/a' },
          'rejected' : { 'fill' : '#C45879', 'font-size' : 12, 'yes-text' : 'n/a', 'no-text' : 'REJECTED' }
        }
      });
    },
    submitCode(graphtype){
      this.subStatus = false
      let content = ""
      let formdata = new FormData()
      if (graphtype==="callGraph") {
        content = this.$refs.cmEditor_callGraph.getValue();
      }else if(graphtype==="AST"){
        content = this.$refs.cmEditor_AST.getValue();
      }else if(graphtype==="sum_code"){
        content = this.$refs.cmEditor_FC.getValue();
      }else if(graphtype==="FC"){
        content = this.$refs.cmEditor_FC.getValue();
      }
      if(content){
        formdata.append('graphtype',graphtype)
        formdata.append('content',content)
        this.graphCodeData.graphtype = graphtype
        this.graphCodeData.content = content
        let languageType = this.cmMode
        if(languageType === 'python'){
          formdata.append('languageName','PYTHON')
          formdata.append('languageId', 3)
          this.graphCodeData.languageName = 'PYTHON'
          this.graphCodeData.languageId = 3
        }else {
          formdata.append('languageName','Others')
          formdata.append('languageId', 0)
          this.graphCodeData.languageName = 'Others'
          this.graphCodeData.languageId = 0
        }

        this.$store.dispatch('topics/submitCodeGraph', formdata).then(data => {
          if(data.success){
            this.subStatus = true
            if (data.data["graphtype"]==="callGraph"){
              this.callGraph_img = data.data["img"]
            }else if(data.data["graphtype"]==="AST"){
              this.AST_img_code = data.data["img"]
              this.ASTcode2graph(this.AST_img_code)
            }else if(data.data["graphtype"]==="sum_code"){
              this.sum_code = data.data["code"]
            }else if(data.data["graphtype"]==="FC"){
              this.FC_img = data.data["img"]
            }
          }
        }).catch(error => {
        })
      }else {
        this.$message.error("代码不能为空")
      }
    },
  }
}
</script>

<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(240, 242, 245);
  position: relative;

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width:1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}

.el-row {
  padding-bottom: 20%;
}
.el-col {
  border-radius: 4px;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}

.question-intr {
  border-radius: 4px;
  border: 1px solid #ddd;
  background: #ebebeb;
  padding: 10px;
  line-height: 1.8;
  margin-bottom: 10px;
  font-size: 14px;
}
.mb2 {
  margin-bottom: 10px;
}
.mt2 {
  margin-top: 10px;
}

.intro {
  font-size: 14px;
  position: relative;
  border-left: 2px solid #1abc9c;
  padding: 10px 0 10px 10px;
  margin: .8em 0;
  background: #fafafa;
  word-break: break-word;
}
.topic_window{
  height: 50%;
  overflow-y: auto;
}
.topic_tool_bar{
  z-index:10;   //保持在上层
  position: fixed;
  left: 0;
  bottom: 0;
  height: 50px;
  width: 51%;
  margin-bottom: 0;
  background: #e6e6e6;
  box-sizing: border-box;
  padding: 12px 20px 12px 15px;
  color: #666666;
  font-size: 14px;
  box-shadow: none;
}
.code_tool_bar{
  z-index:10;   //保持在上层
  position: fixed;
  bottom: 0;
  right: 0;
  margin-left: 5px;
  margin-bottom: 0;
  background: #eeeeee;
  height: 50px;
  width: 49%;
  padding: 12px 20px 12px 15px;
  color: white;
  font-size: 14px;
}
.tool_item{
  margin-right: 10px;
  display: inline-block;
}

.code-mirror-div {
  background-color: black;
  .tool-bar {
    top: 20px;
    margin-left: 20px;
    margin-bottom: 10px;
    margin-top: 10px;
  }
  .code-edit{

  }
}

.image{
  width: auto;
  max-width: 100%;
}



</style>
