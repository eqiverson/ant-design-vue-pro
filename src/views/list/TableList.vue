<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="8" :sm="24">
              <a-form-item label="用户名">
                <a-input v-model="queryParam.key" placeholder="" />
              </a-form-item>
            </a-col>
            <!-- <a-col :md="8" :sm="24">
              <a-form-item label="使用状态">
                <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                  <a-select-option value="0">全部</a-select-option>
                  <a-select-option value="1">关闭</a-select-option>
                  <a-select-option value="2">运行中</a-select-option>
                </a-select>
              </a-form-item>
            </a-col> -->
            <!-- <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="调用次数">
                  <a-input-number v-model="queryParam.callNo" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="更新日期">
                  <a-date-picker v-model="queryParam.date" style="width: 100%" placeholder="请输入更新日期" />
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="使用状态">
                  <a-select v-model="queryParam.useStatus" placeholder="请选择" default-value="0">
                    <a-select-option value=0>全部</a-select-option>
                    <a-select-option value=1>关闭</a-select-option>
                    <a-select-option value=2>运行中</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="使用状态">
                  <a-select placeholder="请选择" default-value="0">
                    <a-select-option value="0">全部</a-select-option>
                    <a-select-option value="1">关闭</a-select-option>
                    <a-select-option value="2">运行中</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
            </template> -->
            <a-col :md="(!advanced && 8) || 24" :sm="24">
              <span
                class="table-page-search-submitButtons"
                :style="(advanced && { float: 'right', overflow: 'hidden' }) || {}"
              >
                <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
                <a-button style="margin-left: 8px" @click="() => (this.queryParam = {})">重置</a-button>
                <!-- <a @click="toggleAdvanced" style="margin-left: 8px">
                  {{ advanced ? '收起' : '展开' }}
                  <a-icon :type="advanced ? 'up' : 'down'" /> -->
                </a>
              </span>
            </a-col>
          </a-row>
        </a-form>
      </div>

      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
        <!-- <a-dropdown  v-if="selectedRowKeys.length > 0">
          <a-menu slot="overlay">
            <a-menu-item key="1"><a-icon type="delete" @click="onDelete(record.key)"/>删除</a-menu-item> -->
            <!-- lock | unlock -->
            <!-- <a-menu-item key="2"><a-icon type="lock" />锁定</a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /> </a-button>
        </a-dropdown> -->
      </div>

      <s-table
        ref="table"
        size="default"
        rowKey="userId"
        :columns="columns"
        :data="loadData"
        :alert="true"
        :rowSelection="rowSelection"
      >
        <!-- <span slot="uid" slot-scope="text, record, index">
          {{ index + 1 }}
        </span> -->
        <!-- <span slot="status" slot-scope="text">
          <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
        </span> -->
        <span slot="status" slot-scope="text">
         {{text | statusFilter}}
        </span>
        <span slot="description" slot-scope="text">
          <ellipsis :length="4" tooltip>{{ text }}</ellipsis>
        </span>
        <span slot="password">
          *********
        </span>

        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleEdit(record)">修改</a>
            <a-divider type="vertical" />
            <!-- <a @click="handleSub(record)">订阅报警</a> -->
            <a-popconfirm v-if="loadData.length" title="确认删除?" @confirm="() => onDelete(record.userId)">
              <a href="javascript:;">删除</a>
            </a-popconfirm>
          </template>
        </span>
      </s-table>

      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk"
      />
      <step-by-step-modal ref="modal" @ok="handleOk" />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis  } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'
import { Register , UpdateUserInfo ,UpdateStatus } from '@/api/login'
import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'






const columns = [
  // {
  //   title: '#',
  //   scopedSlots: { customRender: 'uid' }
  // },
  {
    title: 'userId',
    dataIndex: 'userId'
  },
  {
    title: '用户名',
    dataIndex: 'username'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '姓名',
    dataIndex: 'employeeName'
    // scopedSlots: { customRender: 'description' }
  },
  //  {
  //   title: '性别',
  //   dataIndex: 'gender'
  //  },
  {
    title: '用户密码',
    dataIndex: 'password',
    scopedSlots: { customRender: 'password' }
  },
  {
    title: '手机号',
    dataIndex: 'mobile'
  },
  // {
  //   title: '地市',
  //   dataIndex: 'city'
  // },
  {
    title: '部门',
    dataIndex: 'deptName'
    // needTotal: true,
    // customRender: (text) => text + ' 次'
  },
  // {
  //   title: '专业院',
  //   dataIndex: 'td',
  // },
  {
    title: '状态',
    dataIndex: 'status',
    scopedSlots: { customRender: 'status' },
    filterMultiple: false,
    filters: [
      { text: '正常', value: 0 },
      { text: '停用', value: 1 },
      { text: '已删除', value: 2 },
    ]
  },
  // {
  //   title: '上次登录时间',
  //   dataIndex: 'lastLoginDate',
  //   sorter: true
  // },
  // {
  //   title: '上次登录IP',
  //   dataIndex: 'lastLoginIP'
  // },
  // {
  //   title: '创建时间',
  //   dataIndex: 'createDate',
  //   sorter: true
  // },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]

const statusMap = {
  0: {
    status: '0',
    text: '正常'
  },
  1: {
    status: '1',
    text: '停用'
  },
  2: {
    status: '2',
    text: '已删除'
  },
}

export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    CreateForm,
    StepByStepModal,
  },
  data() {
    this.columns = columns
    return {
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      loadData: (parameter) => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        if(requestParameters.status)
        requestParameters.status=requestParameters.status[0]
        return getRoleList(requestParameters).then((res) => {
          console.log(res)
          return res.data
        })
      },
      selectedRowKeys: [],
      selectedRows: [],
    }
  },
  filters: {
    statusFilter(type) {
      return statusMap[type].text
    },
    statusTypeFilter(type) {
      return statusMap[type].status
    },
  },
  created() {
    getRoleList({})
  },
  computed: {
    rowSelection() {
      return {
        selectedRowKeys: this.selectedRowKeys,
        onChange: this.onSelectChange,
      }
    },
  },
  methods: {
    handleAdd() {
      this.mdl = null
      this.visible = true
    },
    handleEdit(record) {
      this.visible = true
      this.mdl = { ...record }
      console.log(this.mdl)
    },
    onDelete(id) {
      UpdateStatus({userId:id,status:2}).then((res) => {
        this.$message.info(res.message)

      }

      )
      console.log(id)
    },
    handleOk() {
      const form = this.$refs.createModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values)
          if (values.userId > 0) {
            // 修改 e.g.
            UpdateUserInfo(values).then((res) => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info(res.message)
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              Register(values).then((res) => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info(res.message)
            })
          })
        } 
        }else {
          this.confirmLoading = false
        }
      })
    },
    handleCancel() {
      this.visible = false

      const form = this.$refs.createModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    handleSub(record) {
      if (record.status !== 0) {
        this.$message.info(`${record.no} 订阅成功`)
      } else {
        this.$message.error(`${record.no} 订阅失败，规则已关闭`)
      }
    },
    onSelectChange(selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    toggleAdvanced() {
      this.advanced = !this.advanced
    },
    resetSearchForm() {
      this.queryParam = {
        date: moment(new Date()),
      }
    },
  },
}
</script>

<style lang="less" scoped>
.tableHiddle {
  display: none;
}
</style>
