<template>
  <a-modal
    title="用户配置"
    :width="640"
    :visible="visible"
    :confirmLoading="loading"
    @ok="
      () => {
        $emit('ok')
      }
    "
    @cancel="
      () => {
        $emit('cancel')
      }
    "
  >
    <a-spin :spinning="loading">
      <a-form :form="form" v-bind="formLayout">
        <template v-if="model && model.userId > 0">
          <!-- 检查是否有 uid 并且大于0，大于0是修改。其他是新增，新增不显示主键ID -->
          <a-form-item label="userId">
            <a-input v-decorator="['userId']" disabled />
          </a-form-item>
          <a-form-item label="用户名">
            <a-input
              v-decorator="[
                'username',
                { rules: [{ required: true, min: 1, message: '请输入至少一个字符的规则描述！' }] },
              ]"
            />
          </a-form-item>
          <a-form-item label="用户密码">
            <a-input v-decorator="['password', { rules: [{ required: true, message: '请输入规范密码' }] }]" />
          </a-form-item>
          <a-form-item label="手机">
            <a-input v-decorator="['mobile', { rules: [{ required: true, message: '请输入手机号' }] }]" />
          </a-form-item>
          <a-form-item label="部门">
            <a-input v-decorator="['password', { rules: [{ required: false, message: '请输入部门' }] }]" />
          </a-form-item>
          <!--        <a-form-item label="电子邮件">-->
          <!--          <a-input v-decorator="['email', {rules: [{required: true, pattern: /^[a-zA-Z0-9_.-]+@[a-zA-Z0-9-]+(\.[a-zA-Z0-9-]+)*\.[a-zA-Z0-9]{2,6}$/, message: '邮箱格式不正确'}]}]" />-->
          <!--        </a-form-item>-->
          <!--        <a-form-item label="手机号">-->
          <!--          <a-input v-decorator="['phone', {rules: [{required: true, pattern: /[1]+[3456789]+\d{9}/, message: '请输入至少一个字符的规则描述！'}]}]" />-->
          <!--        </a-form-item>-->

          <!-- <a-form-item label="地市">
          <a-input v-decorator="['city', {rules: [{required: true, message: '请输入所属地市'}]}]" />
        </a-form-item> -->
          <a-form-item label="员工名字">
            <a-input v-decorator="['employeeName', { rules: [{ required: true, message: '请输入员工名字' }] }]" />
          </a-form-item>
        </template>

        <template v-else>
          <a-form-item label="userId">
            <a-input v-decorator="['userId', { initialValue: 0 }]" disabled />
          </a-form-item>
          <a-form-item label="用户名">
            <a-input
              v-decorator="[
                'username',
                { rules: [{ required: true, min: 1, message: '请输入至少一个字符的规则描述！' }] },
              ]"
            />
          </a-form-item>
          <a-form-item label="用户密码">
            <a-input v-decorator="['password', { rules: [{ required: true, message: '请输入密码' }] }]" />
          </a-form-item>
          <a-form-item label="员工名字">
            <a-input v-decorator="['employeeName', { rules: [{ required: true, message: '请输入员工名字' }] }]" />
          </a-form-item>
          <a-form-item label="部门">
            <a-input v-decorator="['password', { rules: [{ required: false, message: '请输入部门' }] }]" />
          </a-form-item>
          <a-form-item label="手机">
            <a-input v-decorator="['mobile', { rules: [{ required: true, message: '请输入手机号' }] }]" />
          </a-form-item>
        </template>
        <!-- <a-form-item label="部门">
          <a-input v-decorator="['department', {rules: [{required: true, message: '请输入所属部门'}]}]" />
        </a-form-item>
        <a-form-item label="专业院">
          <a-input v-decorator="['td', {rules: [{required: true, message: '请输入所属专业院'}]}]" />
        </a-form-item> -->
        <!--        <a-form-item label="状态">-->
        <!--          <a-input v-decorator="['state', {initialValue: 1 , rules: [{required: true, pattern: /[1]+[3456789]+\d{9}/, message: '请输入至少一个字符的规则描述！'}]}]" />-->
        <!--        </a-form-item>-->
        <!-- <a-form-item label="创建时间">
          <a-input v-decorator="['createDate',  { initialValue: createDate }]" disabled />
        </a-form-item> -->
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import pick from 'lodash.pick'

// 表单字段
const fields = ['userId', 'username', 'password', 'mobile', 'employeeName', 'deptName']

export default {
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
    loading: {
      type: Boolean,
      default: () => false,
    },
    model: {
      type: Object,
      default: () => null,
    },
  },
  data() {
    this.formLayout = {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 },
      },
    }
    return {
      form: this.$form.createForm(this),
    }
  },
  created() {
    console.log('custom modal created')

    // 防止表单未注册
    fields.forEach((v) => this.form.getFieldDecorator(v))

    // 当 model 发生改变时，为表单设置值
    this.$watch('model', () => {
      this.model && this.form.setFieldsValue(pick(this.model, fields))
    })
  },
}
</script>
