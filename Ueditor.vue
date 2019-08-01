<template>
<div>
    <vue-ueditor-wrap @ready="ready" v-model="msg" :config="ueditorConfig" :destroy="true" @beforeInit="addCustomDialog"></vue-ueditor-wrap>
    <el-dialog :title="title" width="30%" :visible.sync="dialogFnOpenUpload" :close-on-click-modal="false" append-to-body>
      <file-upload :accept="accept" @closeUpload="dialogFnOpenUpload=false"   folderName="safes" :data_extra="data_extra" @fnUploadSucess="fnUploadSucess"  ref="fileUpload"></file-upload>
    </el-dialog>
</div>
</template>

<script>
import VueUeditorWrap from 'vue-ueditor-wrap' // ES6 Module
import config from '@/const/config'
import FileUpload from '@/components/upload/oss-upload'
export default {
  name: '',

  mixins: [],

  components: {VueUeditorWrap, FileUpload},

  props: {
    value: {
      type: String
    },
    config: {
      type: Object
    }
  },

  data () {
    return {
      msg: '',
      ueditorConfig: {
        lang: 'zh-cn',
      // 编辑器不自动被内容撑高
        autoHeightEnabled: false,
      // 初始容器高度
        initialFrameHeight: 240,
      // 初始容器宽度
        initialFrameWidth: '100%',
        wordCount: false,
        elementPathEnabled: false
      // 上传文件接口（这个地址是我为了方便各位体验文件上传功能搭建的临时接口，请勿在生产环境使用！！！）
        // serverUrl: 'http://35.201.165.105:8000/controller.php'
      },
      data_extra: {
        parentId: 0,
        fileName: ''
      },
      title: '添加图片',
      accept: config.accept.picType,
      dialogFnOpenUpload: false,
      editor: null
    }
  },
  computed: {},

  watch: {
    msg: function (newVal, oldVal) {
      if (newVal !== oldVal) {
        this.$emit('input', newVal)
      }
    },
    value: {
      handler: function (newVal, oldVal) {
        if (newVal) {
          this.msg = newVal
        }
      },
      immediate: true
    }
  },

  created () {
    this.ueditorConfig = Object.assign({}, this.ueditorConfig, this.config)
    console.log(this.ueditorConfig)
  },

  mounted () {

  },

  destroyed () {},

  methods: {
    ready (editorInstance) {
      this.editor = editorInstance
    },
    addCustomDialog (editorId) {
      let self = this
      window.UE.registerUI('oss-img', function (editor, uiName) {
    // 参考上面的自定义按钮
        var btn = new window.UE.ui.Button({
          name: 'simpleupload',
          title: '图片',
          onclick: function () {
            self.dialogFnOpenUpload = true
          }
        })

        return btn
      })
      // window.UE.registerUI('xiumi-dialog', function (editor, uiName) {
      //   var btn = new window.UE.ui.Button({
      //     name: 'xiumi-connect',
      //     title: '',
      //     onclick: function () {
      //       var dialog = new window.UE.ui.Dialog({
      //         iframeUrl: './static/UEditor/xiumi-ue-dialog-v5.html?cache=' + Math.random(),
      //         editor: editor,
      //         name: 'xiumi-connect',
      //         title: '',
      //         cssRules: 'width: ' + (window.innerWidth - 60) + 'px;' + 'height: ' + (window.innerHeight - 60) + 'px;'
      //       })
      //       dialog.render()
      //       dialog.open()
      //     }
      //   })

      //   return btn
      // })
    },
       /**
     * @description[fnUploadSucess 提交上传文件函数]
     * @author   tangyuhui
     * @param {Array} uploadFileUrlList [上传文件返回的file对象]
     * @return   {null}   [没有返回]
     */
    fnUploadSucess (uploadFileList) {
      this.editor.focus()
      for (let file of uploadFileList) {
        console.log(file)
        this.editor.execCommand('inserthtml', `<img src=${file.fileUrl}>`)
      }
      this.dialogFnOpenUpload = false
    }
  }
}
</script>

<style>
.edui-button.edui-for-xiumi-connect .edui-button-wrap .edui-button-body .edui-icon {
    background-image: url("http://xiumi.us/connect/ue/xiumi-connect-icon.png") !important;
    background-size: contain;
}
</style>
 