<template>
  <div class="list-container">
    <!-- 头部插槽 -->
    <slot name="header"></slot>
    <div class="list-container-header is-between">
      <ul class="tab-like-box">
        <li
          class="tab-active-bar"
          :class="{ animation: animation }"
          :style="activeBarTransition"
        ></li>
        <li
          v-for="(tab, index) in tabs"
          :key="tab.value"
          class="tab"
          :class="{ 'is-active': currentActive === index }"
          @click="switchTab(tab, $event, index)"
        >
          <span :ref="`tabContent${index}`">{{ tab.label }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "ListContainer",
  props: {
    // example [ { value: 0, label: '这是第一项'}, { value: 1, label: '这是第二项'} ]
    tabs: {
      type: Array,
      // []
      default: () => []
    },
    /**
     * @vuese
     * @param true
     * 是否有动画效果
     */
    animation: {
      type: Boolean,
      // true
      default: true
    },
    value: {
      type: [Number, String],
      default: ""
    }
  },
  data() {
    return {
      activeBarTransition: {},
      defaultActive: 0,

      currentActive: 0
    };
  },
  computed: {
    query() {
      return this.$route.query;
    }
  },
  watch: {
    value: {
      handler(value) {
        const index = this.tabs.findIndex(el => el.value === value);
        if (index > -1) {
          this.currentActive = index;
          this.tabAtiveLine(index);
        }
      },
      immediate: true
    }
  },
  methods: {
    tabAtiveLine(index) {
      // 在dom渲染后执行
      this.$nextTick(() => {
        const firstTab = this.$refs.tabContent0[0];
        const computedFirstTabOffsetLeft = firstTab.offsetLeft;

        const target = this.$refs[`tabContent${index}`][0];
        const tabContentWidth = target.offsetWidth;
        const offsetL = target.offsetLeft;

        this.activeBarTransition = {
          width: `${tabContentWidth + 2 * computedFirstTabOffsetLeft}px`,
          transform: `translateX(${offsetL - computedFirstTabOffsetLeft}px)`
        };
      });
    },
    switchTab(tab, e, index) {
      this.currentActive = index;
      /**
       * @vuese
       * @arg value
       * 选中的 tab 的值
       */
      this.$emit("input", tab.value);

      /**
       * @vuese
       * @arg tab
       * 选中的 tab 对象
       */
      this.$emit("change", {
        ...tab,
        index
      });
    }
  }
};
</script>

<style lang="less">
@import "../../styles/var.less";

.list-container {
  @lineHeight: 60px;

  .list-container-header {
    position: relative;
    display: flex;
    border-bottom: 1px solid #eee;
    background: #fff;
    overflow-x: auto;

    .tab-active-bar {
      position: absolute;
      bottom: 0;
      left: 0;
      z-index: 1;
      width: 80px;
      height: 2px;
      background: @primary-color;

      &.animation {
        transition: 0.3s cubic-bezier(0.645, 0.045, 0.355, 1); // element.ui tabbar 的动画
      }
    }

    .tab-like-box {
      list-style: none;
      padding: 0;
      font-weight: 400;
      display: inherit;
      width: auto;
      white-space: nowrap;

      .tab {
        padding: 0 16px;
        line-height: @lineHeight;
        display: inline-block;
        cursor: pointer;
        color: #000;

        &.is-active {
          color: @primary-color;
        }
      }
    }

    .right-part {
      line-height: @lineHeight;
    }
  }
}
</style>
