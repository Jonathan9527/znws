<template>
  <div>
    <transition name='fade'>
      <ul class="menu-ui"
          :style="{ left:startX.replace(/[^0-9]/ig, '') > innerWidth-150 ? innerWidth - 150 + 'px' : startX, top: startY }"
          v-if="show" @contextmenu="rightMenuClick($event)">

        <li v-for="(item, index) in list" @mouseover="showIndex = index" @mouseleave="showIndex = -1" :key="index">
          <button :disabled="item.disabled" :class="item['divide'] ? 'divide' : ''"
                  @click="emitClickEvent($event, item)" @contextmenu="emitClickEvent($event, item)">

            <i :class="item.headIcon" class="head-icon"></i>
            <span>{{ item.label }}</span>
            <i :class="item.tailIcon" class="tail-icon"></i>

          </button>

          <!--存在子元素进行递归-->
          <right-menu v-if="showChildren(item)" @clickOperate="emitClick" @clickOperateMore="emitClickItem"
                      :list="item.children" :startX="isOverflowX ? '-95%' : '95%'" :startY="'0'"
                      :show="showList(index)"></right-menu>
        </li>

      </ul>
    </transition>
  </div>
</template>

<script>
  export default {
    name: "right-menu",
    props: ['startX', 'startY', 'list', 'show'],
    data() {
      return {
        showIndex: -1,
        innerWidth: '',
        innerHeight: '',
      }
    },
    mounted() {
      let _this = this;
      this.innerWidth = document.documentElement.clientWidth;
      this.innerHeight = document.documentElement.clientHeight;
      $(window).resize(() => {
        _this.innerWidth = document.documentElement.clientWidth;
        _this.innerHeight = document.documentElement.clientHeight;
      })
    },
    computed: {
      isOverflowX() {
        return this.startX.replace(/[^0-9]/ig, '') > this.innerWidth - 300;
      }
    },
    activated() {
    },
    watch: {},
    methods: {
      showChildren(item) {
        return !(!item.children || !item.children.length);
      },
      showList(index) {
        return this.showIndex === index;
      },
      emitClick(item) {
        this.$emit('clickOperate', item);
      },
      emitClickItem(item) {
        this.$emit('clickOperateMore', item);
      },
      emitClickEvent(event, item) {
        if (!this.showChildren(item)) {
          this.emitClick(item.clickIndex);
          this.emitClickItem(item)
        } else {
          event.cancelBubble = true;
          event.stopPropagation();
          event.preventDefault();
        }
      },
      // 阻止默认事件
      rightMenuClick(ev) {
        ev.preventDefault();
      }
    },
  }
</script>

<style scoped="" lang="scss">
  .menu-ui {
    position: absolute;
    background: white;
    width: 150px;
    display: list-item;
    box-shadow: 0 0 1px 0 #6a8dfb;
    list-style: none;
    padding: 13px 0;
    z-index: 1000;
    margin: 0;
  }

  li {
    position: relative;
  }

  button {
    width: 100%;
    background: #ffffff;
    border: none;
    text-decoration: none;
    position: relative;
    display: flex;
    cursor: pointer;
    align-items: center;
    justify-content: space-between;
    padding: 0 10px;
    height: 26px;
    line-height: 26px;
    color: #222;
    &:hover {
      background-color: #6a8dfb;
      color: #ffffff;
    }
  }

  button[disabled] {
    color: #c0c4cc;
    cursor: not-allowed;
    i {
      color: #c0c4cc;
    }
  }

  .divide {
    border-bottom: 1px solid #e6e6e6;
  }

  li:hover {
    background-color: #6a8dfb;
  }

  button > span {
    padding-left: 20px;
    font-size: 13px;
    text-overflow: ellipsis;
    overflow: hidden;
    word-break: break-all;
  }

  i {
    font-size: 13px !important;
  }

  .head-icon {
    position: absolute;
    left: 10px;
    line-height: 26px;
    color: #bfcbfa;
  }

  .tail-icon {
    position: relative;
    float: right;
    line-height: 26px;
    color: #bfcbfa;
  }
</style>
