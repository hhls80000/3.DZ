<template>
  <div id="app">
    <div id="main">
      <header class="header">
        <div class="left">
          <img class="img" src="@/images/logo.png" alt="" />
          <div class="title">东芝客服-小芝</div>
        </div>
      </header>
      <main class="content" ref="record">
        <div
          class="msgLi"
          v-for="item in msgList"
          :key="item.message[0].id ? item.message.id : item.message"
        >
          <dd :class="'img' + (item.isSelf ? 'Right' : 'Left')">
            <i class="i">
              <img
                :src="
                  item.isSelf
                    ? require('@/images/user.png')
                    : require('@/images/r.jpg')
                "
                :class="'img' + (item.isSelf ? 'Right' : 'Left')"
              />
            </i>
            <div class="msgBox">
              <p :class="'send' + (item.isSelf ? 'Right' : 'Left')">
                {{ item.isSelf ? " " : "东芝客服-小芝" }}
                <span v-once>{{
                  (new Date().getHours() >= 10
                    ? new Date().getHours()
                    : "0" + new Date().getHours()) +
                  ":" +
                  (new Date().getMinutes() >= 10
                    ? new Date().getMinutes()
                    : "0" + new Date().getMinutes()) +
                  ":" +
                  (new Date().getSeconds() >= 10
                    ? new Date().getSeconds()
                    : "0" + new Date().getSeconds())
                }}</span>
              </p>
              <span
                id="msgSpan"
                :class="'span' + (item.isSelf ? 'Right' : 'Left')"
              >
                <el-tree
                  v-if="item.message[0].id"
                  class="arrMsg"
                  :data="item.message"
                  :props="defaultProps"
                  accordion
                  auto-expand-parent
                  node-key="item.message.id"
                  icon-class="el-icon-arrow-right"
                  label="item.message.comQuestion"
                  children="item.message.subList"
                  @node-click="handleNodeClick"
                ></el-tree>
                <span class="textMsg" v-else v-html="item.message"></span>
              </span>
            </div>
          </dd>
        </div>
      </main>
      <div class="footer">
        <div class="bar">
          <el-button class="sendButten" @click="dialogVisible = true"
            >留言</el-button
          >
          <el-dialog
            title="留言"
            :visible.sync="dialogVisible"
            width="40%"
            :append-to-body="true"
          >
            <div class="dialog">
              <div style="margin: 20px">
                <p>您好！</p>
                <p>未能快速解决您的问题我们深表歉意。</p>
                <p>请您留下您的联系方式，后续有专业人员回复您的问题。</p>
              </div>
              <el-form
                :label-position="labelPosition"
                label-width="80px"
                :model="ruleForm"
                :rules="rules"
                ref="ruleForm"
              >
                <el-form-item label="姓名" prop="name" class="labelName">
                  <el-input
                    v-model="ruleForm.name"
                    placeholder="姓名"
                  ></el-input>
                </el-form-item>
                <el-form-item label="联系电话" prop="num" class="labelName">
                  <el-input
                    @input="clearRule()"
                    v-model="ruleForm.num"
                    placeholder="手机号或者固定电话号码"
                  ></el-input
                  ><span class="namTip">(联系电话和邮箱，至少填写一种)</span>
                </el-form-item>
                <el-form-item label="邮箱地址" prop="email">
                  <el-input
                    @input="clearRule()"
                    v-model="ruleForm.email"
                    placeholder="邮箱地址如: xxxxx@163.com"
                  ></el-input>
                </el-form-item>
                <el-form-item label="问题概述" prop="desc">
                  <el-input
                    rows="4"
                    type="textarea"
                    v-model="ruleForm.desc"
                    placeholder="请简单描述一下您遇到的问题"
                  ></el-input>
                </el-form-item>
                <el-form-item label="附件">
                  <el-upload
                    class="upload-demo"
                    action="#"
                    :file-list="fileList"
                    list-type="picture"
                    :auto-upload="autoUpload"
                    multiple
                    :limit="limit"
                    :http-request="uploadFile"
                    :on-remove="handleRemove"
                    :on-change="handleChange"
                    :on-exceed="exceed"
                    ref="upload"
                  >
                    <el-button size="small" type="primary">点击上传</el-button>
                    <div slot="tip" class="el-upload__tip" v-html="tip"></div>
                  </el-upload>
                </el-form-item>
                <el-form-item
                  label="验证码"
                  class="identifyCode"
                  prop="identifyCodeValue"
                >
                  <el-input
                    v-model="ruleForm.identifyCodeValue"
                    placeholder="不区分大小写"
                  ></el-input>
                  <div class="getCode" @click="refreshCode()">
                    <s-identify :identifyCode="identifyCode"></s-identify>
                  </div>
                </el-form-item>
                <el-form-item prop="checked">
                  <el-checkbox v-model="ruleForm.checked"
                    >我已阅读《<a href="https://www.toshiba.com.cn/user.html"
                      >个人信息保护政策</a
                    >》</el-checkbox
                  >
                </el-form-item>
              </el-form>
            </div>
            <span slot="footer" class="dialog-footer">
              <el-button @click="cancelForm('ruleForm')">取 消</el-button>
              <el-button
                type="primary"
                @click="submitForm('ruleForm', ruleForm)"
                >提 交</el-button
              >
            </span>
          </el-dialog>
          <div class="evaluation">
            <p>您对机器人的评价?</p>
            <el-rate
              :change="sendRate(rateValue)"
              v-model="rateValue"
              :texts="texts"
              show-text
            >
            </el-rate>
          </div>
        </div>
        <div class="messageBoard">
          <el-input
            type="textarea"
            rows="2"
            v-model="input"
            placeholder="您好！这里是东芝客服部，请详细描述您的问题"
          ></el-input>
          <el-button
            class="sendButten"
            :disabled="disabled"
            @click="sendSelfMsg"
            >发送</el-button
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SIdentify from "@/components/SIdentify.vue";
export default {
  name: "App",
  components: { SIdentify },
  data() {
    return {
      identifyCode: "",
      checkCode: "",
      identifyCodes: "0123456789abcdwerwshdjeJKDHRJHKOOPLMKQ", //绘制的随机数
      disabled: true,
      limit: 3,
      autoUpload: false,
      tip: "",
      msgList: [],
      newData: "",
      value: null,
      defaultProps: {
        children: "subList",
        label: "comQuestion",
      },
      input: "",
      dialogVisible: false,
      formLabelWidth: "120px",
      texts: ["非常不满意", "不满意", "一般满意", "满意", "非常满意"],
      rateValue: null,
      labelPosition: "right",
      ruleForm: {
        name: "",
        num: "",
        desc: "",
        email: "",
        checked: "",
        identifyCodeValue: "",
      },
      fileList: [],
      rules: {
        name: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          {
            validator: function (rule, value, callback) {
              if (!value.trim()) {
                callback(new Error("请输入正确的姓名"));
              } else {
                callback();
              }
            },
            trigger: "blur",
          },
        ],
        num: [
          {
            type: "number",
            message: "请输入正确的手机号或电话号码",
            trigger: "blur",
            transform: (value) => Number(value),
          },
          {
            validator: this.validateRule,
            trigger: "blur",
          },
        ],
        email: [
          {
            type: "email",
            message: "请输入正确的邮箱地址",
            trigger: "blur",
          },
          {
            validator: this.validateRule,
            trigger: "blur",
          },
        ],
        desc: [
          { required: true, message: "请输入问题", trigger: "blur" },
          {
            trigger: "blur",
          },
        ],
        identifyCodeValue: [
          { required: true, message: "请输验证码", trigger: "blur" },
          {
            validator: this.validatorVal,
            trigger: "blur",
          },
        ],
        checked: [
          {
            validator: function (rule, value, callback) {
              console.log(value);
              if (value == false) {
                callback(new Error("请勾选我已阅读"));
              } else {
                callback();
              }
            },
            trigger: "",
          },
        ],
      },
    };
  },
  watch: {
    identifyCode() {
      var that = this;
      that.checkCode = that.identifyCode.toLowerCase();
    },
  },
  created() {
    let msg = "您好，这里是东芝客服部，我是机器人小芝，很高兴为您服务。";
    this.pushMsgList(msg);
    this.getrobotMsg();
    this.checkText();
    this.refreshCode();
  },
  mounted() {},
  //在生命周期updated时，改变并且要在页面重新渲染完成之后
  updated() {
    //  另有一条需要特别注意的地方，注意查看信息调用的接口刷新频率，在接口刷新时，消息列表也会随之刷新，并伴随产生一个小bug就是如果想往回翻看聊天记录的话，接口一刷新进度条就会回到最底部！！！！！！！！！！！！！！！！！！！！！！！！
    // 一般这种事情都是后端解决，如果遇到这种问题可以让后端修改一下即可
    this.$nextTick(() => {
      if (this.isNull(this.input)) {
        this.disabled = false;
      } else {
        this.disabled = true;
      }
      this.$refs.record.scrollTop = this.$refs.record.scrollHeight;
    });
  },
  methods: {
    // https://www.toshiba.com.cn/user.html 用户须知
    // 获取初始数据
    async getrobotMsg() {
      let data = await this.$axios.get(
        "/CallCenter/commonquestion/getQuestionList"
      );
      let list = this._.cloneDeep(data.list);
      for (let i = 0; i < list.length; i++) {
        list[i].subList = this._.flatten(list[i].subList);
      }
      this.pushMsgList(list);
    },

    //树形控件点击事件
    async handleNodeClick(mag) {
      if (mag.parentId) {
        let aa = true;
        this.pushMsgList(mag.comQuestion, aa);
        let param = {
          id: mag.id,
        };
        let data = await this.$axios.post(
          "/CallCenter/dialogue/sendCommonQuestion",
          param
        );
        this.pushMsgList(data.answer);
      }
    },

    //发送按钮事件
    async sendSelfMsg() {
      let param = {
        text: this.input,
      };
      let msg = this.input;
      let aa = true;
      this.pushMsgList(msg, aa);
      this.input = "";
      let data = await this.$axios.post(
        "/CallCenter/dialogue/sendMessage",
        param
      );
      this.pushMsgList(data.answer);
    },

    //添加信息
    pushMsgList(msg, bar) {
      let buer = bar | false;
      let msgs = msg;
      this.msgList.push({
        message: msgs,
        isSelf: buer,
      });
    },

    //点击评价
    async sendRate(val) {
      if (val > 0) {
        let param = {
          evaluate: val,
        };
        let data = await this.$axios.post(
          "/CallCenter/evaluation/userEvaluate",
          param
        );
        console.log(data);
      }
    },

    //判断输入是否为空格或全为空格
    isNull(str) {
      if (!str.trim()) {
        return false;
      }
      return true;
    },

    //改变状态时动态重置校验规则
    validateRule(rule, value, callback) {
      if (!this.ruleForm.num && !this.ruleForm.email) {
        callback(new Error("电话，邮箱地址，请至少填写一项"));
      } else {
        callback();
        // this.$refs.form.clearValidate();
      }
    },

    clearRule() {
      this.$refs.ruleForm.clearValidate("num");
      this.$refs.ruleForm.clearValidate("email");
    },

    //验证码验证
    validatorVal(rule, value, callback) {
      if (value.toLowerCase() === this.checkCode) {
        callback();
      } else {
        callback(new Error("请输入正确的验证码"));
      }
    },

    // 提交表单
    submitForm(formName, form) {
      console.log(form);
      this.$refs[formName].validate((valid) => {
        if (valid) {
          (async () => {
            console.log(form);
            console.log(this.ruleForm);
            let res = await this.uploadFile(form);
            if (res == 200) {
              console.log("ok!");
              this.resetForm(formName);
            }
          })();
        } else {
          console.log("error!!");
          return false;
        }
      });
    },

    resetForm(form) {
      setTimeout(() => {
        this.fileList = [];
        this.$refs[form].resetFields();
        this.dialogVisible = false;
      }, 1000);
    },

    // 取消表单
    cancelForm(form) {
      this.resetForm(form);
      this.$refs.upload.clearFiles();
    },

    //删除文件列表
    handleRemove(fileList) {
      this.checkImg(fileList);
      if (this.fileList.length == 0) {
        this.checkText();
      }
    },
    //文件列表
    handlePreview(file) {
      console.log(file);
    },
    //改变文件列表
    handleChange(file, fileList) {
      this.fileList = fileList;
      this.checkType();
      if (this.fileList.length) {
        this.checkText(3);
      }
    },
    //检查上传图片
    checkType() {
      const whiteList = ["png", "jpg", "gif"];
      for (const i of this.fileList) {
        let extension = i.name.substring(i.name.lastIndexOf(".") + 1);
        // console.log(extension);
        let size = i.size / 1024 / 1024 < 2;
        if (whiteList.indexOf(extension) === -1) {
          this.checkText(0);
          this.checkImg();
          // console.log(this.fileList);
          return false;
        }
        if (!size) {
          this.checkText(1);
          this.checkImg();
          // console.log(this.fileList);
          return size;
        }
      }
    },
    //检查文件格式不让没通过检测的添加
    checkImg(i) {
      if (i) {
        if (this.fileList.indexOf(i) !== -1) {
          this.fileList.splice(this.fileList.indexOf(i), 1);
        }
      } else {
        // 检查文件格式不让添加
        this.fileList = [];
      }
    },

    //检查文件后的文字提示
    checkText(val) {
      switch (val) {
        case 0:
          this.tip = `<span style="color: red">上传图片只能是jpg/png/gif格式!</span>`;
          break;
        case 1:
          this.tip = `<span style="color: red">上传每张图片大小不能超过 2MB!</span>`;
          break;
        case 2:
          this.tip = `<span style="color: red">上传图片不能超过3张</span>`;
          break;
        case 3:
          this.tip = ``;
          break;
        default:
          this.tip = `<span>只能上传jpg/png/gif文件，每张图片不超过2M，且不能超过3张图</span>`;
          break;
      }
    },

    //文件超出个数限制时的钩子
    exceed() {
      this.checkText(2);
      return false;
    },

    //上传
    //参数必须是param，才能获取到内容
    async uploadFile(form) {
      console.log(form, 1111);
      let formData = new FormData(); // 新建一个FormData()对象，这就相当于你新建了一个表单
      Array.from(form).forEach((item) => {
        formData.append("files", item.raw);
      });
      formData.append("mgEmail", form.email);
      formData.append("mgName", form.name);
      formData.append("mgTel", form.num);
      formData.append("mgContent", form.desc);

      let res = await this.$axios({
        url: "/CallCenter/message/addMessage",
        method: "post",
        data: formData,
        headers: {
          "Content-Type": "multipart/form-data",
        },
      });
      return res.code;
    },
    //验证码
    refreshCode() {
      this.identifyCode = "";
      this.makeCode(this.identifyCodes, 4);
    },
    randomNum(min, max) {
      max = max + 1;
      return Math.floor(Math.random() * (max - min) + min);
    },
    // 随机生成验证码字符串
    makeCode(data, len) {
      for (let i = 0; i < len; i++) {
        this.identifyCode += data[this.randomNum(0, data.length - 1)];
      }
    },

    // 销毁时
    beforeDestroy() {},
  },
};
</script>

<style>
a {
  text-decoration: none;
}

a:visited {
  text-decoration: none;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

* {
  padding: 0;
  margin: 0;
}

.all-img {
  width: 40px;
  height: 40px;
}

#main {
  position: fixed;
  top: 50%;
  left: 50%;
  background-color: #ddd;
  width: 986px;
  min-width: 986px;
  min-height: 430px;
  max-height: 720px;
  transform: translate(-50%, -50%);
  overflow: hidden;
}
#main .header {
  height: 62px;
  background: #4b7edc;
  display: flex;
  justify-self: start;
}
#main .header .left {
  font-size: 14px;
  padding: 12px 16px;
  overflow: hidden;
  display: flex;
  align-items: center;
}
#main .header .left .img {
  width: 40px;
  height: 40px;
  vertical-align: middle;
  display: block;
}
#main .header .left .title {
  font-size: 20px;
  font-weight: 550;
  margin-left: 12px;
  overflow: hidden;
  color: #fff;
}

/* 内容区 */
#main .content {
  top: 62px;
  bottom: 121px;
  padding-left: 16px;
  padding-right: 16px;
  overflow-x: hidden;
  -webkit-overflow-scrolling: touch;
  background: #f5f5f5;
  padding-bottom: 20px;
  width: auto;
  max-height: 360px;
  bottom: 217px;
  box-sizing: border-box;
}

.content .msgLi {
  margin-top: 10px;
  padding-left: 10px;
}

.content .msgLi::after,
.content .msgLi .i::after {
  /*添加一个内容*/
  content: "";
  /*转换为一个块元素*/
  display: block;
  /*清除两侧的浮动*/
  clear: both;
}

.content .msgLi dd {
  display: flex;
}

.content .msgLi .msgBox {
  position: relative;
}

.content .msgLi img {
  width: 40px;
  height: 40px;
  margin-top: 5px;
}

.content .msgLi #msgSpan {
  /* background: #7cfc00; */
  border-radius: 10px;
  float: left;
  max-width: 420px;
  box-shadow: 0 0 3px #ccc;
}

.content .msgLi .sendLeft {
  left: 8px;
  text-align: left;
  margin-left: 25px;
  line-height: 1.8;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
}

.content .msgLi .sendRight {
  right: 8px;
  text-align: right;
  margin-right: 25px;
  line-height: 1.8;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
}

.content .msgLi .imgLeft {
  float: left;
}

.content .msgLi .imgRight {
  float: right;
  flex-direction: row-reverse;
  margin-top: 15px;
}

.content .msgLi .spanLeft {
  float: left;
  background: #fff;
  text-align: left;
  margin-left: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.el-tree-node__content {
  flex-direction: row-reverse;
  justify-content: space-between;
}

.content .msgLi .spanLeft::after {
  content: "";
  position: absolute;
  width: 0;
  height: 0;
  top: 34px;
  left: 15px;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-right: 10px solid #fff;
}

.content .msgLi .spanRight {
  float: right;
  background: #7cfc00;
  text-align: left;
  margin-right: 25px;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 15px;
}

.content .msgLi .spanRight::after {
  content: "";
  position: absolute;
  width: 0;
  height: 0;
  top: 33px;
  right: 15px;
  border-top: 8px solid transparent;
  border-bottom: 8px solid transparent;
  border-left: 10px solid #7cfc00;
}

#main .footer {
  background-color: #f5f5f5;
}

#main .footer .bar {
  display: flex;
  justify-content: space-between;
  margin-left: 10px;
  margin-right: 10px;
  padding: 8px 10px;
}

#main .footer .bar .evaluation {
  display: flex;
  align-items: center;
}

#main .footer .bar .evaluation p {
  margin-right: 15px;
  font-size: 13px;
  color: rgba(36, 46, 51, 0.4);
}

#main .footer .messageBoard {
  height: 110px;
  background-color: #fff;
  border-bottom: solid 2px #ddd;
  border-left: solid 1px #ddd;
  border-right: solid 1px #ddd;
  border-top: solid 2px #ddd;
  text-align: right;
}

#main .footer .messageBoard .el-button {
  margin-right: 20px;
}

#main .footer .messageBoard .el-textarea__inner {
  border: none;
}

#main .footer .messageBoard .el-textarea__inner .el-textarea__inner:focus {
  border: none;
}
.identifyCode .el-form-item__content {
  line-height: 60px;
}

.el-form-item__content .getCode {
  display: inline-block;
  width: 120px;
  height: 40px;
  line-height: 40px;
  margin-left: 30px;
  transform: translate(0px, 15px);
  /* border: #fff 5px solid; */
  /* background-color: #ddd; */
}

::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-thumb {
  background-color: #d1d1d1;
  border-radius: 3px;
  -webkit-border-radius: 3px;
  border-left: 2px solid transparent;
  border-top: 2px solid transparent;
}
.dialog {
  width: 100%;
}

.el-dialog__wrapper .el-input {
  width: 60%;
}
.identifyCode .el-input {
  width: 20%;
}

.namTip {
  margin-left: 20px;
  color: rgb(162, 162, 162);
}

.labelName .el-input {
  width: 40%;
}
.el-checkbox a {
  color: rgb(44, 146, 255);
}

.el-textarea__inner {
  resize: none !important;
}

.el-tree-node__label {
  font-size: 14px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* .content .msgLi .spanLeft .el-tree-node__label {
  white-space: pre-wrap;
} */

.el-tree-node > .el-tree-node__children {
  color: #666;
}
.el-tree-node.is-expanded > .el-tree-node__children {
  color: #666;
}

.el-tree-node__content {
  padding: 5px;
  border-bottom: 1px solid #ddd;
}

.el-tree-node__content:hover {
  background-color: #fff;
  color: rgb(35, 124, 240);
}

.el-tree {
  width: 420px;
}

.el-button {
  margin-right: 20px;
}

.el-dialog {
  background-color: rgb(239, 239, 239);
}

.el-dialog__header {
  background-color: #4b7edc;
}

.el-dialog__header .el-dialog__title {
  color: #fff;
  font-weight: 450;
}

.el-dialog__header,
.el-icon-close:before {
  color: #fff;
}

.el-rate {
  position: relative;
}

.el-rate .el-rate__text {
  position: absolute;
  left: -130px;
  width: 130px;
  line-height: 2;
  padding: 1px;
  color: rgba(36, 46, 51, 0.4) !important;
  background-color: #f5f5f5;
  font-size: 13px;
  text-align: center;
  z-index: 99;
}

.el-dialog__header,
.el-icon-close:before {
  color: rgb(177, 177, 177);
}

.el-checkbox-button__inner,
.el-checkbox__input {
  position: relative;
  left: -35px;
}
</style>
