<template>
	<div class="x-custom-form-panel" v-drag="instance" ref="wrap">
    <div class="x-custom-title"><span style="color: #de6c6c;">页面标题为：</span>{{title}}</div>
    <div class="x-custom-form" ref="form"></div>
    <div class="x-custom-answer">
    </div>
  </div>
</template>

<script>
  import GetForm from '../../../libs/script/common';
  import Event from '../../../libs/event';
  
  
  export default {
    name: 'FormPanel',
    data() {
      return {
        instance: this,
        title: '',
      }
    },
    methods: {
      setOffset(offset) {
        window.localStorage.formPanelOffset = offset
      },
      getOffset() {
        return window.localStorage.formPanelOffset
      },
    },
    mounted() {
      this.$refs.wrap.style.cssText = this.getOffset()
      this.$refs.form.appendChild(new GetForm().getForm());
    },
    created() {
      new Event().addEventListener('setTitle', ({detail}) => {
        this.title = detail.title;
      })
    },
    directives: {
      drag: {
        //1.指令绑定到元素上回立刻执行bind函数，只执行一次
        //2.每个函数中第一个参数永远是el，表示绑定指令的元素，el参数是原生js对象
        //3.通过el.focus()是无法获取焦点的，因为只有插入DOM后才生效
        //inserted表示一个元素，插入到DOM中会执行inserted函数，只触发一次
        inserted: function(el, { value: that }) {
          el.onmousedown = function(e) {
            let disx = e.pageX - el.offsetLeft
            let disy = e.pageY - el.offsetTop
            document.onmousemove = function(e) {
              el.style.left = e.pageX - disx + 'px'
              el.style.top = e.pageY - disy + 'px'
              el.style.position = 'absolute'
              that.setOffset(el.style.cssText)
            }
            document.onmouseup = function() {
              document.onmousemove = document.onmouseup = null
            }
          }
        },
        //当VNode更新的时候会执行updated，可以触发多次
      },
    },

  }
</script>

<style scoped>
  .x-custom-title {
    width: 100%;
  }
.x-custom-form-panel {
  width: 500px;
  height: max-content;
  max-height: 94vh;
  border-radius: 10px;
  position: fixed;
  z-index: 99999;
  right: 10px;
  top: 3vh;
  padding: 20px;
  background: white;
  box-shadow: rgba(0, 0, 0, 0.15) 0px 4px 12px;
  box-sizing: border-box;
  overflow: auto;
}

</style>
