<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator" v-if="device === 'desktop'">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('jd_zutai_cat')">导出</a-button>
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

    <!-- table区域-begin -->
    <div>
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
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="device=='desktop'?{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}:null"
        :scroll="{ x: 400 }"
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

        <span slot="action" slot-scope="text, record">
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

    <jdZutaiCat-modal ref="modalForm" @ok="modalFormOk"></jdZutaiCat-modal>
  </a-card>
</template>

<script>
  import {mixinDevice} from '@/utils/mixin.js'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JdZutaiCatModal from './modules/JdZutaiCatModal'

  export default {
    name: "JdZutaiCatList",
    mixins:[JeecgListMixin,mixinDevice],
    components: {
      JdZutaiCatModal
    },
    data () {
      var tmpcol;
      var tt=this._isMobile();
      if(!tt){
        tmpcol=[
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
            title:'类型编号',
            align:"center",
            dataIndex: 'catno'
          },
          {
            title:'类型名称',
            align:"center",
            dataIndex: 'catname'
          },
          {
            title:'类型描述',
            align:"center",
            dataIndex: 'catdesc'
          },
          {
            title:'顺序',
            align:"center",
            dataIndex: 'catorder'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ]
      } else{
        tmpcol=[
          {
            title:'类型编号',
            align:"center",
            width:130,
            dataIndex: 'catno'
          },
          {
            title:'类型名称',
            align:"center",
            width:188,
            dataIndex: 'catname'
          },
          {
            title:'类型描述',
            align:"center",
            dataIndex: 'catdesc'
          }
        ]

      }
      return {
        description: 'jd_zutai_cat管理页面',
        // 表头
        columns: tmpcol,
        url: {
          list: "/jd/jdZutaiCat/list",
          delete: "/jd/jdZutaiCat/delete",
          deleteBatch: "/jd/jdZutaiCat/deleteBatch",
          exportXlsUrl: "/jd/jdZutaiCat/exportXls",
          importExcelUrl: "jd/jdZutaiCat/importExcel",
        },
        dictOptions:{},
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      initDictConfig(){
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>