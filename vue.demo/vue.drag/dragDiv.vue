<template>
  <div class="m_showBox" :class="skin">
    <div class="showBox_mask"></div>
    <div class="loading_wrap" v-if="buttonstatus === 1"></div>
    <div class="pop_box" id="pox-box" v-drag>
      <p class="pop_box_title">
        {{title || "提示"}}
        <span class="pop_box_close" @click="cancel"></span>
      </p>
      <div class="pop_box_content">
        <slot></slot>
      </div>
      <div class="pop_box_bottom">
        <a href="javascript:;"
           class="cancel_btn"
           @click="cancel">{{canceltext || "取消"}}</a>
        <a href="javascript:;"
           class="confirm_btn"
           v-if="type === 'confirm'"
           :class="{widths: buttonstatus === 1}"
           @click="confirm">
          <svg viewBox="25 25 50 50" class="u-circular" v-if="buttonstatus === 1">
            <circle cx="50" cy="50" r="20" fill="none" class="path"></circle>
          </svg>
          <span :class="{'marginLeft': buttonstatus === 1}">{{confirmtext || '确定'}}</span>
        </a>
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    // 由父组件传入的值
    props: ["skin", "title", "type", "confirmtext", "canceltext", "buttonstatus"],
    data: function () {
      return {};
    },
    watch: {},
    mounted: function () {
    },
    methods: {
      cancel: function () {
        this.$emit("cancel");
      },
      confirm: function () {
        if (this.buttonstatus === 1) {
          return;
        }
        this.$emit("confirm");
      },
    },
    directives: {
      drag: {
        inserted: function (el, binding, vnode) {
          vnode = vnode.elm;
          el.onmousedown = ((event) => {
            if (event.target.className !== "pop_box_title") {
              return;
            }
            //获取鼠标在盒子中的位置
            let mouseX = event.clientX - vnode.offsetLeft;
            let mouseY = event.clientY - vnode.offsetTop;
            //绑定移动和停止函数
            document.onmousemove = ((event) => {
              let left, top;
              //获取新的鼠标位置对应下的盒子应该在的位置
              left = event.clientX - mouseX;
              top = event.clientY - mouseY;
              //获取div在页面中X轴的最小最大位置
              let minX = vnode.offsetWidth / 2;
              let maxX = (window.innerWidth - vnode.offsetWidth / 2) - 10//去掉滚动条的宽度
              if (left <= minX) {
                left = minX;
              } else if (left >= maxX) {
                left = maxX;
              }
              //获取div在页面中Y轴的最大最小位置
              let minY = vnode.offsetHeight / 2;
              let maxY = (window.innerHeight - vnode.offsetHeight / 2);
              if (top <= minY) {
                top = minY;
              } else if (top >= maxY) {
                top = maxY;
              }
              //赋值移动
              vnode.style.left = left + 'px';
              vnode.style.top = top + 'px';
            });
            document.onmouseup = (() => {
              document.onmousemove = document.onmouseup = null;
            });
          });
          window.onresize = (() =>{
            vnode.style.left = "50%";
            vnode.style.top = "50%";
          });
        }
      }
    }
  }
</script>
<style>
  .m_showBox {
    position: fixed;
    z-index: 99;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
  }

  .loading_wrap {
    position: fixed;
    z-index: 10000000000;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    background: transparent;
  }

  .m_showBox .showBox_mask {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    background-color: #000;
    opacity: 0.5;
  }

  .pop_box {
    position: absolute;
    width: 550px;
    background: #fff;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .m_showBox .pop_box_content {
    min-height: 100px;
    max-height: 450px;
    overflow-x: hidden;
    overflow-y: auto;
    position: relative;
    padding-top: 24px;
    padding-bottom: 21px;
  }

  .m_showBox .pop_box_title {
    cursor: all-scroll;
    word-break: keep-all;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    position: relative;
    top: 0;
    left: 0;
    height: 40px;
    line-height: 40px;
    background: #e7e8eb;
    color: #000;
    font-size: 18px;
    font-family: \5fae\8f6f\96c5\9ed1;
    padding: 0 31px 0 18px;
    text-align: left;
    user-select: none;
  }

  .m_showBox .pop_box_title .pop_box_close {
    cursor: pointer;
    position: absolute;
    top: 50%;
    margin-top: -8px;
    right: 20px;
    width: 16px;
    height: 16px;
    line-height: 999em;
    overflow: hidden;
    background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAAABmJLR0QAAAAAAAD5Q7t/AAAACXBIWXMAAAsSAAALEgHS3X78AAABmUlEQVQ4y33TsWoVURAG4O9ucbGJGixsFMktNHhtbOwsElbIciRdWguNhahFat9BBVEQog+ghaBZN+ABER/ARsSIBi30YiGSNOpttMisLDFmYIpzZv6Zf/4zp2eblXV1HIuoMBXX61jFck7Nm25+rwPs4zoWcBMreBfhaczjCh5hKafmx98CAa6xgQs5NRt2sLKu9uI+JlHl1IyLiN0I8ML/wJBTsxkMvwdbvbKuhsg4llOzWdbVNZzC+Zyab9F5KjT4nFMzG0ze4kwRgt2J6jAT8z4r6+pAgJ/jKPZ3mNzCYoE5PO4wPYc1nMSL8CNxd7aTt4K5AoOg0875JVisY4jDeI/ZiLW2hkFhZ9uDfuf8E7+25fQxLqLTdOep2pkP4UP4iVaTToEBPhWh7nwn8LQz8+nwVpO7nbyEXOAeLsXTwAivMJNTM8qpGYUmq3jZWairWG438TYOxiL9touVddXDQ3zNqbnciriEfXhQ1tXELuCJAE8GZtfP9CRm78cSpaD972fa1mWIi7YWbIAxPtpa9+Wcmtfd/D8G7JBwDhffdwAAAABJRU5ErkJggg==) no-repeat;
  }

  .m_showBox .pop_box_bottom {
    margin: 0;
    padding: 16px 0;
    text-align: center;
    border-top: 1px solid transparent;
    background: #e7e8eb;
  }

  .pop_box_bottom .cancel_btn {
    display: inline-block;
    min-width: 60px;
    height: 28px;
    line-height: 28px;
    background: #ffffff;
    margin: 0 8px;
    padding: 0 15px;
    border: 1px solid #e6e7ec;
    color: #222222;
    text-align: center;
    vertical-align: middle;
    font-size: 14px;
    text-decoration: none;
    border-radius: 3px;
    cursor: pointer;
  }

  .pop_box_bottom .cancel_btn {
    display: inline-block;
    min-width: 60px;
    height: 28px;
    line-height: 28px;
    background: #ffffff;
    margin: 0 8px;
    padding: 0 15px;
    border: 1px solid #e6e7ec;
    color: #222222;
    text-align: center;
    vertical-align: middle;
    font-size: 14px;
    text-decoration: none;
    border-radius: 3px;
    cursor: pointer;
  }

  .pop_box_bottom .confirm_btn {
    position: relative;
    display: inline-block;
    min-width: 60px;
    height: 28px;
    line-height: 28px;
    background: #44b549;
    margin: 0 8px;
    padding: 0 15px;
    border: 1px solid #44b549;
    color: #fff;
    text-align: center;
    vertical-align: middle;
    font-size: 14px;
    text-decoration: none;
    border-radius: 3px;
    cursor: pointer;
  }

  .pop_box_bottom .cancel_btn:hover {
    background: #e6e7ec;
    border-color: #dadbe0;
  }

  .pop_box_bottom .confirm_btn:hover {
    background: #2f9833;
    border-color: #2f9833;
  }

  .u-circular {
    position: absolute;
    right: 60px;
    top: 4px;
    width: 20px;
    height: 20px;
    animation: loading-rotate 2s linear infinite;
  }

  .u-circular .path {
    animation: loading-dash 1.5s ease-in-out infinite;
    stroke-dasharray: 90, 150;
    stroke-dashoffset: 0;
    stroke-width: 2;
    stroke: #fff;
    stroke-linecap: round;
  }

  .pop_box_bottom .confirm_btn .widths {
    width: 70px;
  }

  .pop_box_bottom .confirm_btn .marginLeft {
    margin-left: 10px;
  }

  /*skin样式宽度*/
  .small .pop_box {
    width: 550px;
  }

  .middle .pop_box {
    width: 700px;
  }

  .large .pop_box {
    width: 850px;
  }

</style>

