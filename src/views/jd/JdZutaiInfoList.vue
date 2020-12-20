<template>

  <a-card :bordered="false" >
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery2">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="分类">
              <j-dict-select-tag v-model="queryParam.ztcat" placeholder="请选择分类" dictCode="jd_zutai_cat,catname,catno,catno!='' order by catno"/>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery2" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset2" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator" v-if="device === 'desktop'">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('jd_zutai_info')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin    :style="{'width':device=='desktop'?'100%':'100%'}"  -->
    <div >
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;" v-if="device === 'desktop'">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource1"
        :scroll="{ x: 400 }"

        :pagination="ipagination"
        :loading="loading"
        :rowSelection="device=='desktop'?{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}:null"
        
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="图片不存在" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="uploadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record" >
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>


    </div>

    <jdZutaiInfo-modal ref="modalForm" @ok="modalFormOk" v-if="device === 'desktop'"></jdZutaiInfo-modal>
  </a-card>

</template>

<script>

  import {mixinDevice} from '@/utils/mixin.js'
  import { JeecgListMixin2 } from '@/mixins/JeecgListMixin2'
  import JdZutaiInfoModal from './modules/JdZutaiInfoModal'
  import JEllipsis from '@/components/jeecg/JEllipsis'
  export default {
    name: "JdZutaiInfoList",
    mixins:[JeecgListMixin2,mixinDevice],
    components: {
      JdZutaiInfoModal,
      JEllipsis
    },
    data () {
      var tmpcol;
      var tt=this._isMobile();
      if(!tt){
        tmpcol= [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'编号',
            align:"center",
            dataIndex: 'ztno'
          },
          {
            title:'名称',
            align:"center",
            dataIndex: 'ztname'
          },
          {
            title:'显示值',
            align:"center",
            dataIndex: 'ztvalue'
          },
          {
            title:'类型',
            align:"center",
            dataIndex: 'ztcat'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ];
      }else{
        tmpcol= [
        {
          title:'名称',
          align:"center",
          width:200,
          dataIndex: 'ztname'
        },
        {
          title:'显示值',
          align:"center",
          width:120,
          dataIndex: 'ztvalue'
        },
        {
          title:'类型',
          align:"center",
//          width:80,
          dataIndex: 'ztcat'
        }];

      }
      return {
        description: 'jd_zutai_info管理页面',
        // 表头
        columns: tmpcol,
        dataSource1:[],
        url: {
          list: "/jd/jdZutaiInfo/list",
          delete: "/jd/jdZutaiInfo/delete",
          deleteBatch: "/jd/jdZutaiInfo/deleteBatch",
          exportXlsUrl: "/jd/jdZutaiInfo/exportXls",
          importExcelUrl: "jd/jdZutaiInfo/importExcel",
        },

        dictOptions:{},
      }
    },
    created() {
      this.mock();
    },
    mounted(){
//      this.mock();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      mock() {
        if(typeof(this.$route.query.cat)=='string'){
          var sq = this.$route.query.cat;
          if(sq!='Alarm'){
            this.dataSource1 = this.$root.analogList.filter(item => item.ztcat === sq);
//            this.ipagination.total = Math.ceil(this.dataSource1.length/this.ipagination.pageSize);
            this.ipagination.total=this.dataSource1.length;
            this.ipagination.current = 1;
          }else {
            this.dataSource1 = this.$root.analogList.filter(item => item.ztno.indexOf(sq)>-1);
//            this.ipagination.total = Math.ceil(this.dataSource1.length/this.ipagination.pageSize);
            this.ipagination.total=this.dataSource1.length;
            this.ipagination.current = 1;
          }
        }else{
          this.dataSource1=this.$root.analogList
        }
        this.ipagination.total=this.dataSource1.length;
      },

      searchQuery2(){
        var sq = this.queryParam.ztcat;
        if(sq!=null){
          this.dataSource1 = this.$root.analogList.filter(item => item.ztcat === sq);
//          this.ipagination.total = Math.ceil(this.dataSource1.length/this.ipagination.pageSize);
          this.ipagination.total=this.dataSource1.length;
          this.ipagination.current = 1;
        }

      },
      searchReset2() {
        this.queryParam = {};
        this.dataSource1=this.$root.analogList;
        this.ipagination.total = this.dataSource1.length;
        this.ipagination.current = 1;
      },
      initDictConfig(){
      },
/*      _isMobile() {
        let flag = navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i)
        return flag;
      }
*/
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>