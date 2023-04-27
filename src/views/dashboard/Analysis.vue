<template>
  <div class="main-home-div">
	  <div class="top-main-bg">
		  <div class="top-main-bg-div">
		  		欢迎回来，{{ nickname() }}
		  </div>
	  </div>
    <index-chart></index-chart>
<!--    <index-bdc v-if="indexStyle==2"></index-bdc>-->
<!--    <index-task v-if="indexStyle==3"></index-task>-->
  </div>
</template>

<script>
  import IndexChart from './IndexChart'
  import IndexTask from "./IndexTask"
  import IndexBdc from './IndexBdc'
  import { mapGetters } from 'vuex'

  export default {
    name: "Analysis",
    components: {
      IndexChart,
      IndexTask,
      IndexBdc
    },
    data() {
      return {
        indexStyle:1
      }
    },
    created() {

    },
    methods: {
      ...mapGetters(["nickname", "avatar"]),

    }
  }
</script>
<style>
	.top-main-bg{
		z-index: 2;
		height: 59px;
		transition: background 300ms;
		background-image: url(../../assets/login_images/image2.jpg);
		background-repeat: no-repeat;
		background-size: 100% auto;
		background-position: center -28vw;
		width: calc(100% + 24px);
		margin-left: -12px;
		margin-top: -12px;
		margin-bottom: 15px;
	}
	.hascollapsed .top-main-bg{
		z-index: 2;
		height: 65px;
		transition: background 300ms;
		background-image: url(../../assets/login_images/image2.jpg);
		background-repeat: no-repeat;
		background-size: 100% auto;
		background-position: center -29.8vw;
		width: calc(100% + 24px);
		margin-left: -12px;
		margin-top: -12px;
		margin-bottom: 15px;
	}
	.top-main-bg-div{
		width: 100%;
		background-color: rgba(0, 0, 0, 0.3);
		height: 100%;
		line-height: 65px;
		color: #FFF;
		padding-left: 1rem;
		font-size: 20px;
	}
  @media only screen and (max-width: 1000px) and (min-width: 800px) {
    .top-main-bg{
      z-index: 2;
      height: 59px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -33.2vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .hascollapsed .top-main-bg{
      z-index: 2;
      height: 65px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -33.2vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .top-main-bg-div{
      width: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      height: 100%;
      line-height: 65px;
      color: #FFF;
      padding-left: 1rem;
      font-size: 20px;
    }
  }
  @media only screen and (max-width: 1300px) and (min-width: 1000px) {
    .top-main-bg{
      z-index: 2;
      height: 59px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      /* background-position: center -26.5vw; */
      background-position: center -18.5vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .hascollapsed .top-main-bg{
      z-index: 2;
      height: 65px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -29.8vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .top-main-bg-div{
      width: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      height: 100%;
      line-height: 65px;
      color: #FFF;
      padding-left: 1rem;
      font-size: 20px;
    }
  }
  @media only screen and (max-width: 1500px) and (min-width: 1300px) {
    .top-main-bg{
      z-index: 2;
      height: 59px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      /* background-position: center -27.5vw; */
      background-position: center -19.5vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .hascollapsed .top-main-bg{
      z-index: 2;
      height: 65px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -30vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .top-main-bg-div{
      width: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      height: 100%;
      line-height: 65px;
      color: #FFF;
      padding-left: 1rem;
      font-size: 20px;
    }
  }

  @media only screen and (max-width: 1800px) and (min-width: 1500px) {
    body {
      background: #162887;
    }
    .top-main-bg{
      z-index: 2;
      height: 59px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -28vw;
      background-position: center -18.2vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .hascollapsed .top-main-bg{
      z-index: 2;
      height: 65px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -29.8vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .top-main-bg-div{
      width: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      height: 100%;
      line-height: 65px;
      color: #FFF;
      padding-left: 1rem;
      font-size: 20px;
    }
  }
  @media only screen and (max-width: 2200px) and (min-width: 1800px) {
    .top-main-bg{
      z-index: 2;
      height: 59px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      /* background-position: center -28.2vw; */
      background-position: center -18.2vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .hascollapsed .top-main-bg{
      z-index: 2;
      height: 65px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -29.8vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .top-main-bg-div{
      width: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      height: 100%;
      line-height: 65px;
      color: #FFF;
      padding-left: 1rem;
      font-size: 20px;
    }
  }
  @media only screen and (min-width: 2200px) {
    .top-main-bg{
      z-index: 2;
      height: 59px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -19.2vw;
      /* background-position: center -29vw; */
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .hascollapsed .top-main-bg{
      z-index: 2;
      height: 65px;
      transition: background 300ms;
      background-image: url(../../assets/login_images/image2.jpg);
      background-repeat: no-repeat;
      background-size: 100% auto;
      background-position: center -30.8vw;
      width: calc(100% + 24px);
      margin-left: -12px;
      margin-top: -12px;
      margin-bottom: 15px;
    }
    .top-main-bg-div{
      width: 100%;
      background-color: rgba(0, 0, 0, 0.3);
      height: 100%;
      line-height: 65px;
      color: #FFF;
      padding-left: 1rem;
      font-size: 20px;
    }
  }
</style>