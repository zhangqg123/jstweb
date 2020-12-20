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

        <a-form-item label="设备名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devicename', validatorRules.devicename]" placeholder="请输入设备名称"></a-input>
        </a-form-item>
        <a-form-item label="设备编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'deviceno', validatorRules.deviceno]" placeholder="请输入设备编号"></a-input>
        </a-form-item>
        <a-form-item label="顺序" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'deviceorder', validatorRules.deviceorder]" placeholder="请输入顺序"></a-input>
        </a-form-item>
        <a-form-item label="模型编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-popup
            v-decorator="['devicemodel', validatorRules.devicemodel]"
            :trigger-change="true"
            org-fields="modelno"
            dest-fields="devicemodel"
            code="model"
            @callback="popupCallback"/>
        </a-form-item>
        <a-form-item label="X轴" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devicex', validatorRules.devicex]" placeholder="请输入X轴"></a-input>
        </a-form-item>
        <a-form-item label="Y轴" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devicey', validatorRules.devicey]" placeholder="请输入Y轴"></a-input>
        </a-form-item>
        <a-form-item label="Z轴" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'devicez', validatorRules.devicez]" placeholder="请输入Z轴"></a-input>
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
    name: "TjDeviceModal",
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
          devicename: {rules: [
          ]},
          deviceno: {rules: [
          ]},
          deviceorder: {rules: [
          ]},
          devicemodel: {rules: [
          ]},
          devicex: {rules: [
          ]},
          devicey: {rules: [
          ]},
          devicez: {rules: [
          ]},
        },
        url: {
          add: "/tj/tjDevice/add",
          edit: "/tj/tjDevice/edit",
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
          this.form.setFieldsValue(pick(this.model,'devicename','deviceno','deviceorder','devicemodel','devicex','devicey','devicez'))
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
        this.form.setFieldsValue(pick(row,'devicename','deviceno','deviceorder','devicemodel','devicex','devicey','devicez'))
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