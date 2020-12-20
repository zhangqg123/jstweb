<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="设备名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devName', validatorRules.devName]" placeholder="请输入设备名称"></a-input>
        </a-form-item>
        <a-form-item label="设备分类" :labelCol="labelCol" :wrapperCol="wrapperCol">
      <!--    <a-input v-decorator="[ 'devCat', validatorRules.devCat]" placeholder="请输入设备分类"></a-input> -->
          <j-popup
            v-decorator="['devCat', validatorRules.devType]"
            :trigger-change="true"
            org-fields="origin_id"
            dest-fields="devCat"
            code="jstzccat"
            @callback="popupCallback"/>
        </a-form-item>
        <a-form-item label="设备编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devNo', validatorRules.devNo]" placeholder="请输入设备编号"></a-input>
        </a-form-item>
        <a-form-item label="模型编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'modNo', validatorRules.modNo]" placeholder="请输入模型编号"></a-input>
        </a-form-item>
        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
<!--          <a-input v-decorator="[ 'status', validatorRules.status]" placeholder="请输入状态"></a-input>  -->
          <j-dict-select-tag  v-decorator="['status', {}]" :triggerChange="true" placeholder="请输入设备状态"
                              dictCode="ac_status"/>
        </a-form-item>
        <a-form-item label="位置" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'position', validatorRules.position]" placeholder="请输入位置"></a-input>
        </a-form-item>
        <a-form-item label="扩展信息" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'extInfo', validatorRules.extInfo]" placeholder="请输入扩展信息"></a-input>
        </a-form-item>
        <a-form-item label="连接信息" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'conInfo', validatorRules.conInfo]" placeholder="请输入连接信息"></a-input>
        </a-form-item>
        <a-form-item label="ip地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'ipAddress', {}]" placeholder="请输入ip地址"></a-input>
        </a-form-item>
        <a-form-item label="端口" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'port', {}]" placeholder="请输入端口号"></a-input>
        </a-form-item>
        <a-form-item label="连接类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'type', {}]" placeholder="请输入连接类型"></a-input>
        </a-form-item>
        <a-form-item label="重试" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'retry', {}]" placeholder="请输入重试次数"></a-input>
        </a-form-item>
        <a-form-item label="休眠时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sleeptime', {}]" placeholder="请输入休眠时间"></a-input>
        </a-form-item>
        <a-form-item v-if="contype==='SOCKET'"  label="从机编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'slave', {}]" placeholder="请输入从机编号"></a-input>
        </a-form-item>
        <a-form-item v-if="contype==='SNMP'" label="版本号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'version', {}]" placeholder="请输入版本号"></a-input>
        </a-form-item>
        <a-form-item v-if="contype==='SOCKET'" label="超时" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sotimeout', {}]" placeholder="请输入超时时间"></a-input>
        </a-form-item>
        <a-form-item v-if="contype==='SNMP'" label="超时" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'timeOut', {}]" placeholder="请输入超时时间"></a-input>
        </a-form-item>
        <a-form-item label="属性信息" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'proInfo', validatorRules.proInfo]" placeholder="请输入属性信息"></a-input>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'remark', validatorRules.remark]" placeholder="请输入备注"></a-input>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JTreeSelect from '@/components/jeecg/JTreeSelect'

  export default {
    name: "JstZcDevModal",
    components: {
      JTreeSelect
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        contype: null,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
          devName: {rules: [
          ]},
          devCat: {rules: [
          ]},
          devNo: {rules: [
          ]},
          modNo: {rules: [
            ]},
          position: {rules: [
          ]},
          extInfo: {rules: [
          ]},
          conInfo: {rules: [
          ]},
          proInfo: {rules: [
          ]},
          remark: {rules: [
          ]},
        },
        url: {
          add: "/jst/jstZcDev/add",
          edit: "/jst/jstZcDev/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        if(!record.id){
          this.visible = true;
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,'devName','devCat','devNo','modNo','status','position','type','extInfo','conInfo','proInfo','remark'))
          })
        }else{
          var tempInfo=JSON.parse(this.model.conInfo);
          this.model.ipAddress=tempInfo.ipAddress;
          this.model.port=tempInfo.port;
          this.model.type=tempInfo.type;
          this.model.retry=tempInfo.retry;
          this.model.sleeptime=tempInfo.sleeptime;
          if(this.model.type=="SOCKET"){
            this.contype="SOCKET";
            this.model.slave=tempInfo.slave;
            this.model.sotimeout=tempInfo.sotimeout;
          }
          if(this.model.type=="SNMP"){
            this.contype="SNMP";
            this.model.version=tempInfo.version;
            this.model.timeOut=tempInfo.timeOut;
          }
          this.visible = true;
          if(this.model.type=="SOCKET"){
            this.$nextTick(() => {
              this.form.setFieldsValue(pick(this.model,'devName','devCat','devNo','modNo','status','position','extInfo','conInfo','ipAddress','port','type','slave','retry','sleeptime','sotimeout','proInfo','remark'))
            })
          }else{
            this.$nextTick(() => {
              this.form.setFieldsValue(pick(this.model,'devName','devCat','devNo','modNo','status','position','extInfo','conInfo','ipAddress','port','type','retry','sleeptime','timeOut','proInfo','remark','version'))
            })
          }
        }
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            var tip=values.ipAddress;
            var tport=values.port;
            let tempConInfo={};
            if(values.conInfo){
              tempConInfo=JSON.parse(values.conInfo);
            }
            tempConInfo.ipAddress=tip;
            tempConInfo.port=tport;
            tempConInfo.type=values.type;
            tempConInfo.retry=values.retry;
            tempConInfo.sleeptime=values.sleeptime;

            if(values.type=="SOCKET"){
              tempConInfo.slave=values.slave;
              tempConInfo.sotimeout=values.sotimeout;
            }
            if(values.type=="SNMP"){
              tempConInfo.version=values.version;
              tempConInfo.timeOut=values.timeOut;
            }

            let tempInfo=JSON.stringify(tempConInfo);
            values.conInfo=tempInfo;
            let formData = Object.assign(this.model, values);

            console.log("表单提交数据:",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }

        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'devName','devCat','devNo','modNo','position','extInfo','conInfo','proInfo','remark'))
      },


    }
  }
</script>