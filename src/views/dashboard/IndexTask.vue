<template>
  <div class="index-container-ty">
    <a-spin :spinning="loading">
      <!-- <a-row type="flex" justify="start" :gutter="3"> -->
      <a-row   >
        <a-col :sm="24" :lg="12">
          <a-card>
            <div slot="title" class="index-md-title">
              <img src="../../assets/daiban.png"/>
              温湿度【{{ length1 }}】
            </div>
            <div slot="extra">
              <a v-if="dataSource1 && dataSource1.length>0" slot="footer" @click="goPage('TH')">更多 <a-icon type="double-right" /></a>
            </div>
            <a-table
              :class="'my-index-table tytable1'"
              ref="table1"
              size="small"
              rowKey="id"
              :columns="columns1"
              :loading="loading"
              :dataSource="dataSource1"
              :pagination="false">
              <template slot="ellipsisText" slot-scope="text">
                <j-ellipsis :value="text" :length="textMaxLength"></j-ellipsis>
              </template>

              <template slot="dayWarnning" slot-scope="text,record">
                <a-icon type="bulb" theme="twoTone" style="font-size:22px" :twoToneColor="getTipColor(record)"/>
              </template>

              <span slot="action" slot-scope="text, record">
                <a @click="handleData">办理</a>
              </span>

            </a-table>
          </a-card>
        </a-col>

        <a-col :sm="24" :lg="12">
          <a-card>
            <div slot="title" class="index-md-title">
              <img src="../../assets/zaiban.png"/>
              空调【{{ length2 }}】
            </div>
            <div slot="extra">
              <a v-if="dataSource2 && dataSource2.length>0" slot="footer" @click="goPage('JMAC')">更多 <a-icon type="double-right" /></a>
            </div>
            <a-table
              :class="'my-index-table tytable2'"
              ref="table2"
              size="small"
              rowKey="id"
              :columns="columns1"
              :dataSource="dataSource2"
              :pagination="false">

              <template slot="ellipsisText" slot-scope="text">
                <j-ellipsis :value="text" :length="textMaxLength"></j-ellipsis>
              </template>

              <template slot="dayWarnning" slot-scope="text,record">
                <a-icon type="bulb" theme="twoTone" style="font-size:22px" :twoToneColor="getTipColor(record)"/>
              </template>

              <span slot="action" slot-scope="text, record">
                <a @click="handleData">办理</a>
              </span>

            </a-table>
          </a-card>
        </a-col>

        <a-col :span="24" v-if="device === 'desktop'">
          <div style="height: 5px;"></div>
        </a-col>

        <a-col :sm="24" :lg="12">
          <a-card>
            <div slot="title" class="index-md-title">
              <img src="../../assets/guaz.png"/>
              UPS【{{ length3 }}】
            </div>
            <div slot="extra">
              <a v-if="dataSource4 && dataSource4.length>0" slot="footer" @click="goPage('UPS')">更多 <a-icon type="double-right" /></a>
            </div>

            <a-table
              :class="'my-index-table tytable4'"
              ref="table4"
              size="small"
              rowKey="id"
              :columns="columns1"
              :dataSource="dataSource3"
              :pagination="false">
              <template slot="ellipsisText" slot-scope="text">
                <j-ellipsis :value="text" :length="textMaxLength"></j-ellipsis>
              </template>

              <template slot="dayWarnning" slot-scope="text,record">
                <a-icon type="bulb" theme="twoTone" style="font-size:22px" :twoToneColor="getTipColor(record)"/>
              </template>

              <span slot="action" slot-scope="text, record">
                <a @click="handleData">办理</a>
              </span>

            </a-table>
          </a-card>
        </a-col>

        <a-col :sm="24" :lg="12">
          <a-card>
            <div slot="title" class="index-md-title">
              <img src="../../assets/duban.png"/>
              报警【{{ length4 }}】
            </div>
            <div slot="extra">
              <a v-if="dataSource4 && dataSource4.length>0" slot="footer" @click="goPage('Alarm')">更多 <a-icon type="double-right" /></a>
            </div>
            <a-table
              :class="'my-index-table tytable3'"
              ref="table3"
              size="small"
              rowKey="id"
              :columns="columns1"
              :dataSource="dataSource4"
              :pagination="false">
              <template slot="ellipsisText" slot-scope="text">
                <j-ellipsis :value="text" :length="textMaxLength"></j-ellipsis>
              </template>

              <template slot="dayWarnning" slot-scope="text,record">
                <a-icon type="bulb" theme="twoTone" style="font-size:22px" :twoToneColor="getTipColor(record)"/>
              </template>

              <span slot="action" slot-scope="text, record">
                <a @click="handleData">办理</a>
              </span>

            </a-table>
          </a-card>
        </a-col>

      </a-row>
    </a-spin>

  </div>
</template>

<script>
  import noDataPng from '@/assets/nodata.png'
  import JEllipsis from '@/components/jeecg/JEllipsis'
  import { mixinDevice } from '@/utils/mixin.js'

//  import { getAction } from '../../api/manage'



  //4-7天
  const tip_green = "rgba(0, 255, 0, 1)"
  //1-3天
  const tip_yellow = "rgba(255, 255, 0, 1)"
  //超期
  const tip_red = "rgba(255, 0, 0, 1)"

  export default {
    name: "IndexTask",
    mixins:[mixinDevice],
    components:{ JEllipsis },
    data() {
      var tmpcol;
      var tt = this._isMobile();
      if (tt) {
        tmpcol = [
          {
            title: '',
            dataIndex: '',
            key: 'rowIndex',
            width: 50,
            fixed: 'left',
            align: "center",
            scopedSlots: { customRender: "dayWarnning" }
          },
          {
            title: '名称',
            align: "center",
            dataIndex: 'ztname',
            width: 150
          },
          {
            title: '显示值',
            align: "center",
            dataIndex: 'ztvalue',
            width:150,
            scopedSlots: { customRender: "ellipsisText" }
          },
          {
            title: '编号',
            align: "center",
            dataIndex: 'ztno',
          }
        ];

      }else{
        tmpcol = [
          {
            title: '',
            dataIndex: '',
            key: 'rowIndex',
            width: 50,
            fixed: 'left',
            align: "center",
            scopedSlots: { customRender: "dayWarnning" }
          },
          {
            title: '名称',
            align: "center",
            dataIndex: 'ztname',
            width: 150
          },
          {
            title: '显示值',
            align: "center",
            dataIndex: 'ztvalue',
//            width:100,
            scopedSlots: { customRender: "ellipsisText" }
          },
          {
            title: '编号',
            align: "center",
            dataIndex: 'ztno',
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
//            width:100,
            scopedSlots: { customRender: 'action' }
          }
        ];
      }
      return {
        loading:false,
        textMaxLength:8,
        length1:0,
        length2:0,
        length3:0,
        length4:0,
        dataSource1:[],
        dataSource2:[],
        dataSource3:[],
        dataSource4:[],
        columns1: tmpcol,
        url: {
          list: "/jd/jdZutaiInfo/list"
        }
      }
    },
    created() {
      this.mock();
    },

    methods: {

      getTipColor(rd){
        let num = rd.ztvalue
//        if(num>=40) {
//          return tip_red
//        }else if(num>=4){
//        }else{
          return tip_green
//        }
      },
      goPage(ztcat){
        this.$router.push({ path: '/jd/jdZutaiInfoList?cat='+ztcat })
      },
      mock(){
        this.dataSource1=[]
        this.dataSource2=[]
        this.dataSource3=[]
        this.dataSource4=[]
//        this.ifNullDataSource(this.dataSource4,'.tytable4')
        var count1=0;
        var count2=0;
        var count3=0;
        var count4=0;
        var raList=this.$root.analogList;

        if(raList.length>0){
          for(var i=0;i<raList.length;i++){
            var ra=raList[i];
            if(ra.ztcat=="TH"){
              if(count1<5 && ra.ztcat=="TH"){
                this.dataSource1.push(ra);
              }
              count1++;
            }
            if(ra.ztcat=="JMAC"){
              if(count2<5 && ra.ztcat=="JMAC"){
                this.dataSource2.push(ra);
              }
              count2++;
            }
            if(ra.ztcat=="UPS"){
              if(count3<5 && ra.ztcat=="UPS"){
                this.dataSource3.push(ra);
              }
              count3++;
            }
            if(ra.ztno.indexOf('Alarm')>-1){
              if(count4<5 && ra.ztno.indexOf('Alarm')>-1){
                this.dataSource4.push(ra);
              }
              count4++;
            }

//            if(count1>4&&count2>4&&count3>4&&count4>4){
//              break;
//            }
          }
          this.length1=count1;
          this.length2=count2;
          this.length3=count3;
          this.length4=count4;
        }

      },
      _isMobile() {
        let flag = navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i)
        return flag;
      },
      ifNullDataSource(ds,tb){
        this.$nextTick(()=>{

          if(!ds || ds.length==0){
            var tmp = document.createElement('img');
            tmp.src=noDataPng
            tmp.width=300
            let tbclass=`${tb} .ant-table-placeholder`
            document.querySelector(tbclass).innerHTML=""
            document.querySelector(tbclass).appendChild(tmp)
          }
        })
      },
      handleData(){
        this.$message.success("办理完成")
      }




    }
  }
</script>

<style>
  .my-index-table{height:270px;}
  .my-index-table table{font-size: 14px !important;}

  .index-container-ty .ant-card-head-title{padding-top: 6px;padding-bottom: 6px;}
  .index-container-ty .ant-card-extra{padding:0}
  .index-container-ty .ant-card-extra a{color:#fff}
  .index-container-ty .ant-card-extra a:hover{color:#152ede}
  .index-container-ty .ant-card-head-wrapper,.index-container-ty .ant-card-head{
    line-height:24px;
    min-height:24px;
    /*background: #90aeff;*/
    background: #7196fb;
  }
  .index-container-ty .ant-card-body{padding: 10px 12px 0px 12px}

  /* .index-container-ty .ant-card-actions{background: #fff}
   .index-container-ty .ant-card-actions li {margin:2px 0;}
   .index-container-ty .ant-card-actions > li > span{width: 100%}*/


  .index-container-ty .ant-table-footer{text-align: right;padding:6px 12px 6px 6px;background: #fff;border-top: 2px solid #f7f1f1;}

  .index-md-title{
    postion:relative;
    padding-left:24px;
    width: 100%;
    color: #fff;
    font-size: 21px;
    font-family: cursive;
  }
  .index-md-title img{
    position: absolute;
    height:32px;
    top: 2px;
    left:14px;
  }

  .index-container-ty .ant-card-body{
    /*border-left:1px solid #90aeff;
    /*border-right:1px solid #90aeff;
    border-bottom:1px solid #90aeff;*/
  }


  .index-container-ty .ant-table-thead > tr > th,
  .index-container-ty .ant-table-tbody > tr > td{
    border-bottom: 1px solid #90aeff;
  }

  .index-container-ty .ant-table-small > .ant-table-content > .ant-table-fixed-left > .ant-table-body-outer > .ant-table-body-inner > table > .ant-table-thead > tr > th,
  .index-container-ty .ant-table-small > .ant-table-content > .ant-table-fixed-right > .ant-table-body-outer > .ant-table-body-inner > table > .ant-table-thead > tr > th{
    border-bottom: 1px solid #90aeff;
  }

  .index-container-ty  .ant-table-small > .ant-table-content > .ant-table-scroll > .ant-table-body > table > .ant-table-thead > tr > th{
    border-bottom: 1px solid #90aeff;
  }

  .index-container-ty .ant-table-small{
    border: 1px solid #90aeff;
  }

  .index-container-ty .ant-table-placeholder {
    padding: 0
  }
</style>