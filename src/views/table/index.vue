<template>
  <div class="app-container">
    
        <!--表单-->
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item label="用户名称">
        <el-input v-model="formInline.name" placeholder="用户名称" style="width: 140px;"></el-input>
      </el-form-item>
      <el-form-item label="楼盘名称">
        <el-input v-model="formInline.kind" placeholder="楼盘名称" style="width: 140px;"></el-input>
      </el-form-item>
      <el-form-item label="收款时间">
        <el-date-picker type="date" placeholder="到期时间" v-model="formInline.endTime" style="width: 100%;" ></el-date-picker>
      </el-form-item>
      <el-button type="primary" @click="onSubmit">查询</el-button>
      <el-button type="primary" @click="addFormVisible = true">添加</el-button>
    </el-form>

    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
      :span-method="objectSpanMethod"
    >
      <el-table-column align="center" label="序号" width="95" >
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column>
      <el-table-column label="楼盘名称">
        <template slot-scope="scope">
          {{ scope.row.kind }}
        </template>
      </el-table-column>
      <el-table-column label="位置">
        <template slot-scope="scope">
          {{ scope.row.position }}
        </template>
      </el-table-column>
      <el-table-column label="用户名称" width="110" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.name }}</span>
        </template>
      </el-table-column>
      <el-table-column label="数量" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.num }}
        </template>
      </el-table-column>
      <el-table-column label="规格" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.size }}
        </template>
      </el-table-column>
      
      <el-table-column label="发布时间" width="220" align="center" >
        <template slot-scope="scope">
            <el-date-picker type="date" placeholder="" v-model="scope.row.startTime" :readonly="true" ></el-date-picker>
        </template>
      </el-table-column>

      <el-table-column label="收款时间" width="220" align="center">
        <template slot-scope="scope">
          <div  style="display:flex">
            <span v-if="scope.row.isWarning">
              <svg t="1566890379761" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2272" width="40" height="40"><path d="M846 511.905c0 184.391-149.516 333.869-333.904 333.869-184.354 0-333.869-149.479-333.869-333.869C178.227 327.517 327.742 178 512.096 178 696.484 178 846 327.517 846 511.905z" fill="#E8373D" p-id="2273"></path><path d="M481.412 341.655h73.938v88.223l-19.168 162.307h-35.164l-19.604-162.307-0.002-88.223z m1.676 278.66h70.623v68.219h-70.623v-68.219z" fill="#FFFFFF" p-id="2274"></path></svg>
            </span>
            <el-date-picker type="date" placeholder="到期时间" v-model="scope.row.endTime" :readonly="true" ></el-date-picker>
          </div>
        </template>
      </el-table-column>
      
      <el-table-column label="操作"  width="180">
        <template scope="scope">
          <el-button type="primary" size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button type="danger" size="small" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
        <!-- <el-table-column class-name="status-col" label="Status" width="110" align="center">
          <template slot-scope="scope">
            <el-tag :type="scope.row.status | statusFilter">{{ scope.row.status }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column align="center" prop="created_at" label="Display_time" width="200">
          <template slot-scope="scope">
            <i class="el-icon-time" />
            <span>{{ scope.row.display_time }}</span>
          </template>
        </el-table-column> -->
    </el-table>

    <el-dialog title="编辑信息" :visible="dialogFormVisible" size="tiny">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="楼盘名称">
          <el-input v-model="form.kind"></el-input>
        </el-form-item>
        <el-form-item label="位置">
          <el-input v-model="form.position"></el-input>
        </el-form-item>
        <el-form-item label="用户名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="数量">
          <el-input v-model="form.size"></el-input>
        </el-form-item>
        <el-form-item label="发布时间">
         <el-date-picker type="date" placeholder="到期时间" v-model="form.startTime"  ></el-date-picker>
        </el-form-item>
        <el-form-item label="收款时间">
         <el-date-picker type="date" placeholder="到期时间" v-model="form.endTime"  ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSave" :loading="editLoading">修改</el-button>
          <el-button @click="dialogFormVisible = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
    <el-dialog title="新增信息" :visible="addFormVisible" size="tiny">
      <el-form ref="addForm" :model="addForm" label-width="80px">
        <el-form-item label="楼盘名称">
          <el-input v-model="addForm.kind"></el-input>
        </el-form-item>
        <el-form-item label="位置">
          <el-input v-model="addForm.position"></el-input>
        </el-form-item>
        <el-form-item label="用户名称">
          <el-input v-model="addForm.name"></el-input>
        </el-form-item>
        <el-form-item label="数量">
          <el-input v-model="addForm.size"></el-input>
        </el-form-item>
        <el-form-item label="发布时间">
         <el-date-picker type="date" placeholder="到期时间" v-model="addForm.startTime" ></el-date-picker>
        </el-form-item>
        <el-form-item label="收款时间" >
         <el-date-picker type="date" placeholder="到期时间" v-model="addForm.endTime" ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleAdd" :loading="addLoading">添加</el-button>
          <el-button @click="addFormVisible = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import { getList } from '@/api/table'

export default {
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'gray',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      list: [
        {
          id: 1,
          kind: "电梯广告",
          position: "位置",
          name: "用户名称1",
          num: "数量1",
          size: "规格2",
          startTime: new Date(2000,4,5,17),
          endTime: new Date(2000,4,5,17)
        },
        {
          id: 2,
          kind: "电梯广告",
          position: "位置",
          name: "用户名称2",
          num: "数量2",
          size: "规格3",
          startTime: new Date(2000,4,5,17),
          endTime: new Date(2000,4,5,17)
        },
        {
          id: 3,
          kind: "桁架广告",
          position: "位置1",
          name: "用户名称3",
          num: "数量3",
          size: "规格4",
          startTime: new Date(2000,4,5,17),
          endTime: new Date(2000,4,5,17)
        },
        {
          id: 4,
          kind: "桁架广告",
          position: "位置2",
          name: "用户名称4",
          num: "数量4",
          size: "规格5",
          startTime: new Date(2000,4,5,17),
          endTime: new Date(2000,4,5,17)
        },
        {
          id: 5,
          kind: "桁架广告",
          position: "位置3",
          name: "用户名称5",
          num: "数量5",
          size: "规格6",
          startTime: new Date(2000,4,5,17),
          endTime: new Date(2000,4,5,17)
        },
      ],
      listLoading: true,
      kind: ["电梯广告", "桁架广告", "驻点广告"],
      dialogFormVisible: false,
      addFormVisible: false,
      addLoading: false,
      form: {},
      addForm: {
        kind: "电梯广告",
        position: "位置",
        name: "用户名称",
        num: "数量",
        size: "规格",
        startTime: new Date(),
        endTime: new Date()
      },
      editLoading: false,
      formInline: {
        name: '',
        kind: '',
        endTime: ''
      },
    }
  },
  created() {
    this.fetchData()
  },
  mounted() {
    
  },
  methods: {
    onSubmit () {
      this.$message('未完成');
      console.log(this.formInline.endTime)
    },
    fetchData() {
      this.listLoading = true
      getList().then(response => {
        // this.list = response.data.items
        this.listLoading = false
      })
    },
    handleDelete (index, row) {
      this.list.splice(index, 1);
      this.$message({
        message: "操作成功！",
        type: 'success'
      });
    },
    handleEdit (index, row) {
      this.dialogFormVisible = true;
      this.form = Object.assign({}, row);
      this.table_index = index;
    },
    handleAdd() {
      this.$confirm('确认提交吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        cancelButtonClass: 'cancel'
      }).then(() => {
        let id = this.list.length + 1
        let form = this.addForm
        form.id = id
        this.list.push(form)
        this.$message({
          message: "操作成功！",
          type: 'success'
        });
        this.addLoading = false;
        this.addFormVisible = false;
      }).catch(() => {

      });
    },
    handleSave () {
      this.$confirm('确认提交吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        cancelButtonClass: 'cancel'
      }).then(() => {
        this.editLoading = true;
        // let date = this.form.date;
        // if (typeof date === "object") {
        //   date = [date.getFullYear(), (date.getMonth() + 1), (date.getDate())].join('-');
        //   this.form.date = date
        // }
//          this.tableData[this.table_index] = this.form;
        this.list.splice(this.table_index, 1, this.form);
        this.$message({
          message: "操作成功！",
          type: 'success'
        });
        this.editLoading = false;
        this.dialogFormVisible = false;
      }).catch(() => {

      });
    },
    //合并单元格函数(未完成)
    objectSpanMethod({ row, column, rowIndex, columnIndex }) {
      if (columnIndex === 1) {
        // console.log(row, column, rowIndex, columnIndex)
        // if(this.list[rowIndex-1]){
        //   let boolen = this.list[rowIndex].kind === this.list[rowIndex-1].kind
        //   console.log(this.list[rowIndex].kind)
        //   console.log(this.list[rowIndex-1].kind)
        // }else {

        // }
        return {
          rowspan:1,
          colspan:1
        }
        // if (rowIndex % 2 === 0) {
        //   return {
        //     rowspan: 2,
        //     colspan: 1
        //   };
        // } else {
        //   return {
        //     rowspan: 0,
        //     colspan: 0
        //   };
        // }
      }
    },
  }
}
</script>

<style>
  .active {
    background-color: red;
  }
</style>