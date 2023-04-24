<template>
  <div class="footer">
<!--    <div class="links">
      <a href="http://www.jeecg.com" target="_blank">JEECG 首页</a>
      <a href="https://github.com/zhangdaiscott/jeecg-boot" target="_blank">
        <a-icon type="github"/>
      </a>
      <a href="https://ant.design/">Ant Design</a>
      <a href="https://vuecomponent.github.io/ant-design-vue/docs/vue/introduce-cn/">Vue Antd</a>
    </div>-->
    <!--水印-->
    <div id="show">

    </div>
<!--    <div class="copyright">-->
<!--      Copyright-->
<!--      <a-icon type="copyright"/>-->
<!--      2022-->
<!--    </div>-->
  </div>
</template>

<script>
  import watermark from '@/utils/watermark'
  export default {
    name: "LayoutFooter",
    mounted() {
      watermark.set(this.$store.getters.userInfo.realname,this.$store.getters.userInfo.username)

    },
  }

</script>

<style lang="less" scoped>
  .footer {
    padding: 0 16px;
    margin: 48px 0 24px;
    text-align: center;

    .links {
      margin-bottom: 8px;

      a {
        color: rgba(0, 0, 0, .45);

        &:hover {
          color: rgba(0, 0, 0, .65);
        }

        &:not(:last-child) {
          margin-right: 40px;
        }
      }
    }
    .copyright {
      color: rgba(0, 0, 0, .45);
      font-size: 14px;
    }
  }
</style>