<template>
<el-form class='key-content-string'>
  <!-- key content textarea -->
  <el-form-item>
    <FormatViewer
      ref='formatViewer'
      :content.sync='content'
      :binary='binary'
      float=''
      :textrows=12>
    </FormatViewer>
  </el-form-item>

  <!-- save btn -->
  <el-form-item>
    <el-button type="primary" @click="execSave">{{ $t('message.save') }}</el-button>
  </el-form-item>
</el-form>
</template>

<script>
import FormatViewer from '@/components/FormatViewer';

export default {
  data() {
    return {
      content: Buffer.from(''),
      binary: false,
    };
  },
  props: ['client', 'redisKey'],
  components: { FormatViewer },
  methods: {
    initShow() {
      this.client.getBuffer(this.redisKey).then((reply) => {
        this.content = reply;
        this.$refs.formatViewer.autoFormat();
      });
    },
    execSave() {
      const key = this.redisKey;

      this.client.set(
        key,
        this.content
      ).then((reply) => {
        if (reply === 'OK') {
          this.initShow()

          this.$message.success({
            message: this.$t('message.modify_success'),
            duration: 1000,
          });
        }

        else {
          this.$message.error({
            message: this.$t('message.modify_failed'),
            duration: 1000,
          });
        }
      });
    },
  },
  mounted() {
    this.initShow();
  },
};
</script>

<style type="text/css">
  .key-content-string .format-viewer-container {
    min-height: calc(100vh - 253px);
  }

  /*text viewer box*/
  .key-content-string .el-textarea textarea {
    font-size: 14px;
    height: calc(100vh - 286px);
  }
  /*not text viewer box, such as json*/
  .key-content-string .text-formated-container {
    box-sizing: border-box;
    min-height: calc(100vh - 286px);
  }
</style>
