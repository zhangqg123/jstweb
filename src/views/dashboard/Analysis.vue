<template>
  <div>
    <index-task v-if="indexStyle==1"></index-task>

    <index-chart v-if="indexStyle==2"></index-chart>
    <index-bdc v-if="indexStyle==3"></index-bdc>
<!--
    <div style="width: 100%;text-align: right;margin-top: 20px">
      请选择首页样式：
      <a-radio-group v-model="indexStyle">
        <a-radio :value="1">任务表格</a-radio>
        <a-radio :value="2">统计图表</a-radio>
        <a-radio :value="3">统计图表2</a-radio>

      </a-radio-group>
    </div>
-->
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
        indexStyle:null
      }
    },
    created() {
      this.mock();
    },
    methods: {
      ...mapGetters(["userInfo"]),
      mock(){
        let username = this.userInfo().username;
        console.log("userinfo:"+this.userInfo());
        if(username=="jdyc" || username=="fengye"){
          this.indexStyle=1;
        }else{
          this.indexStyle=2;
        }
      }
    }
  }
</script>