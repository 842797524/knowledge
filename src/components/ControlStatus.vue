<template>
  <div class="x-custom-status-panel-wrap" ref="wrap" v-drag="instance">
    <div @click="changeMode" class="x-custom-status">
      <div :class="{'x-custom-status-active': eventType === 'click', 'x-custom': true}" data-mode="click">点击</div>
      <div :class="{'x-custom-status-active': eventType === 'input','x-custom': true}" data-mode="input">输入</div>
      <div :class="{'x-custom-status-active': eventType === 'select', 'x-custom': true}" data-mode="select">下拉</div>
      <div :class="{'x-custom-status-active': eventType === 'drag','x-custom': true}" data-mode="drag">拖动</div>
    </div>
  </div>
</template>

<script>
  import { message } from 'ant-design-vue'
  import 'ant-design-vue/dist/antd.css'
  import Event from '../../../libs/event'
  
  const event = new Event()
  export default {
    name: 'Status',
    data() {
      
      return {
        instance: this,
        eventType: 'click',
      }
    },
    methods: {
      changeMode(e) {
        if (!e.target.dataset.mode) return
        this.eventType = e.target.dataset.mode
        
        this.keydown(null, true)
      },
      keydown(e, isClick) {
        let flag = isClick || false
        if (!isClick) {
          if (+e.key === 2 && e.altKey) {
            this.eventType = 'input' // 输入 默认为 alt + 2
            flag = true
          } else if (e.code === 'Digit3' && e.altKey) {
            // 下拉框模式 默认为 alt + 3
            this.eventType = 'select'
            flag = true
            
          } else if (e.key === '1' && e.altKey) {
            this.eventType = 'click'
            flag = true
            
          } else if (e.code === 'Digit4' && e.altKey) {
            this.eventType = 'drag'
            flag = true
          }
        }
        
        const enumType = {
          click: '点击',
          select: '下拉',
          input: '输入',
          drag: '拖动',
        }
        if (enumType[this.eventType] && flag) {
          message.info('当前模式为' + enumType[this.eventType])
          event.dispatchEvent('changeMode', {
            detail: {
              mode: this.eventType,
            },
          })
        }
      },
      setOffset(offset) {
        window.localStorage.offset = offset
      },
      getOffset() {
        return window.localStorage.offset
      },
    },
    mounted() {
      this.$refs.wrap.style.cssText = this.getOffset()
      window.addEventListener('keydown', this.keydown)
    },
    beforeDestroy() {
      window.removeEventListener('keydown', this.keydown)
    },
    directives: {
      drag: {
        //1.指令绑定到元素上回立刻执行bind函数，只执行一次
        //2.每个函数中第一个参数永远是el，表示绑定指令的元素，el参数是原生js对象
        //3.通过el.focus()是无法获取焦点的，因为只有插入DOM后才生效
        //inserted表示一个元素，插入到DOM中会执行inserted函数，只触发一次
        inserted: function(el, { value: that }) {
          el.onmousedown = function(e) {
            var disx = e.pageX - el.offsetLeft
            var disy = e.pageY - el.offsetTop
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
  .x-custom-status-panel-wrap {
    user-select: none;
    left: 100px;
    top: 100px;
    position: fixed !important;
    z-index: 9999999999;
    font-size: 14px;
  }
  
  .x-custom-status {
    --width: 50px;
    display: grid;
    grid-template-columns: repeat(auto-fill, var(--width));
    justify-content: center;
    gap: 10px;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    padding: 10px 20px;
    width: 280px;
    height: 70px;
    position: absolute;
    border-radius: 5px;
    background: white;
    
    
  }
  
  .x-custom-status > div {
    width: var(--width);
    height: var(--width);
    border-radius: 50%;
    background: #fff;
    border: 1px solid #d9d9d9;
    color: rgba(0, 0, 0, 0.65);
    cursor: pointer;
    
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .x-custom-status .x-custom-status-active {
    background: #40a9ff;
    color: white;
  }
</style>
