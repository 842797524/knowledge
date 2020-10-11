<template>
  <div>
    <a-button @click.ctrl="next" ref="button" @click="showRecord" class="x-custom-next-button" icon="safety-certificate" title="按钮已被代理" type="primary">{{nextText}}
    </a-button>
  </div>
</template>
<script>
  import Vue from 'vue'
  import { Button, message} from 'ant-design-vue'
  import config from '../../../libs/config';
  
  import 'ant-design-vue/dist/antd.css'
  // 点击下一步的时候弹窗显示当前页面选中的数据
  Vue.use(Button)
  export default {
    name: 'NextButtonHook',
    data() {
      return {
        nextText: '下一步'
      }
    },
    components: {
      'a-button': Button,
    },
    methods: {
      next(e) {
        //
        let nextSelector = config.site[location.hostname].next;
        if (nextSelector) {
          if ($(e.target).offsetParent().hasClass('x-custom-form-panel')) {
            nextSelector = '.x-custom-form-panel ' + nextSelector;
          }
          document.querySelector(nextSelector).click();
          this.event.dispatchEvent('clickNextPage', {});
        }else {
          message.info('没有找到下一步的按钮');
        }
      },
      showRecord() {
        message.info('成功的记录已被标红，请再次进行检查');
        this.event.dispatchEvent('verifyData', {detail: {verify:true}});
      },
      setEvent(event) {
        this.event =event;
      },
      setNextText(text) {
        this.nextText = text;
      }
    },

  
  }
</script>

<style scoped>
  .x-custom-next-button {
    width: var(--x-custom-next-button-width);
    height: var(--x-custom-next-button-height, 30px);
    left: var(--x-custom-next-button-left);
    top: var(--x-custom-next-button-top);
    position: absolute;
    z-index: 999999999999999999;
    display: flex;
    justify-content: center;
    align-items: center;
    
  }
 
</style>
