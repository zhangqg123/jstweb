<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="指标名称">
              <j-input placeholder="请输入指标名称" v-model="queryParam.targetName"></j-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="报警点">
              <!--            <a-input placeholder="请输入模型编号" v-model="queryParam.devicemodel"></a-input>  -->
              <j-dict-select-tag v-model="queryParam.alarmPoint" placeholder="请选择报警点" dictCode="alarm_point"/>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="设备类型">
              <!--            <a-input placeholder="请输入模型编号" v-model="queryParam.devicemodel"></a-input>  -->
              <j-dict-select-tag v-model="queryParam.devType" placeholder="请选择分类" dictCode="jst_zc_cat,zc_catname,origin_id ,has_child ='0'&&id>'002' "/>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
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
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('jst_zc_target')">导出</a-button>
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
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
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
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"

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

    <jstZcTarget-modal ref="modalForm" @ok="modalFormOk"></jstZcTarget-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JstZcTargetModal from './modules/JstZcTargetModal'
  import JInput from '@/components/jeecg/JInput.vue';

  export default {
    name: "JstZcTargetList",
    mixins:[JeecgListMixin],
    components: {
      JstZcTargetModal,JInput
    },
    data () {
      return {
        model: {},
        description: 'jst_zc_target管理页面',
        // 表头
        columns: [
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
            title:'设备类型',
            align:"center",
            dataIndex: 'devType_dictText'
          },
/*          {
            title:'指标编码',
            align:"center",
            dataIndex: 'targetNo'
          }, */
          {
            title:'指标名称',
            align:"center",
            dataIndex: 'targetName'
          },
/*          {
            title:'单位',
            align:"center",
            dataIndex: 'unit'
          },

          {
            title:'协议',
            align:"center",
            dataIndex: 'proType'
          },
*/
          {
            title:'报警点',
            align:"center",
            dataIndex: 'alarmPoint_dictText'
          },
          {
            title:'指令',
            align:"center",
            dataIndex: 'instruct'
          },
          {
            title:'寄存器地址',
            align:"center",
            dataIndex: 'address'
          },
          {
            title:'点类型',
            align:"center",
            dataIndex: 'infoType'
          },
          {
            title:'报警值',
            align:"center",
            dataIndex: 'ctrlUp'
          },
          {
            title:'报警表达式',
            align:"center",
            dataIndex: 'ctrlDown'
          },
          {
            title:'计算因子',
            align:"center",
            dataIndex: 'yinzi'
          },

          /*         {
            title:'数据类型',
            align:"center",
            dataIndex: 'dataType_dictText'
          },  */
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/jst/jstZcTarget/list?column=dev_type&order=asc",
          delete: "/jst/jstZcTarget/delete",
          deleteBatch: "/jst/jstZcTarget/deleteBatch",
          exportXlsUrl: "/jst/jstZcTarget/exportXls",
          importExcelUrl: "jst/jstZcTarget/importExcel",
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