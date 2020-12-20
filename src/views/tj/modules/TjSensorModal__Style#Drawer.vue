<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
    @close="close"
    :visible="visible">
  
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="传感器名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sensorname', validatorRules.sensorname]" placeholder="请输入传感器名称"></a-input>
        </a-form-item>
        <a-form-item label="传感器编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sensorno', validatorRules.sensorno]" placeholder="请输入传感器编号"></a-input>
        </a-form-item>
        <a-form-item label="顺序" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sensororder', validatorRules.sensororder]" placeholder="请输入顺序"></a-input>
        </a-form-item>
        <a-form-item label="模型编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'sensormodel', validatorRules.sensormodel]" placeholder="请输入模型编号"></a-input>
        </a-form-item>
        <a-form-item label="设备编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-popup
            v-decorator="['sensordevice', validatorRules.sensordevice]"
            :trigger-change="true"
            org-fields="deviceno,devicemodel"
            dest-fields="sensordevice,sensormodel"
            code="device"j-dict-select-tag
            @callback="popupCallback"/>
        </a-form-item>
        
      </a-form>
    </a-spin>
    <a-button type="primary" @click="handleOk">确定</a-button>
    <a-button type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  
  export default {
    name: "TjSensorModal",
    components: { 
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
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
          sensorname: {rules: [
          ]},
          sensorno: {rules: [
          ]},
          sensororder: {rules: [
          ]},
          sensormodel: {rules: [
          ]},
          sensordevice: {rules: [
          ]},
        },
        url: {
          add: "/tj/tjSensor/add",
          edit: "/tj/tjSensor/edit",
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
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'sensorname','sensorno','sensororder','sensormodel','sensordevice'))
        })
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
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
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
        this.form.setFieldsValue(pick(row,'sensorname','sensorno','sensororder','sensormodel','sensordevice'))
      }
      
    }
  }
</script>

<style lang="less" scoped>
/** Button按钮间距 */
  .ant-btn {
    margin-left: 30px;
    margin-bottom: 30px;
    float: right;
  }
</style>