<template>
  <!-- 表格的公共组件，需传入三个数据，操作栏返回父组件两个方法 -->
  <div class="table-list">
    <div class="list">
      <div class="list-table">
        <el-table :data="UpShowTableData" style="width: 100%">
          <el-table-column prop="address" label="日期" min-width="180">
          </el-table-column>
          <el-table-column
            v-for="(item, index) in tableHeader"
            :key="index"
            :prop="item.prop"
            :label="item.label"
            min-width="180"
          >
          </el-table-column>
          <el-table-column
            v-if="deleteEdit.options == 1"
            label="操作"
            fixed="right"
            min-width="180"
          >
            <template slot-scope="scope">
              <el-button
                v-show="deleteEdit.delete == 1"
                size="mini"
                type="danger"
                @click="handleEditDelete(scope.$index, scope.row)"
                >删除
              </el-button>
              <el-button
                v-show="deleteEdit.edit == 1"
                size="mini"
                type="warning"
                @click="handleEdit(scope.$index, scope.row)"
                >编辑
              </el-button>
            </template>
          </el-table-column>
        </el-table>
        <div class="pagination">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="[8, 15, 20]"
            layout="total, sizes, prev, pager, next, jumper"
            :total="upTotal"
          >
          </el-pagination>
        </div>
      </div>
      <!-- 弹出框表单 -->
      <div class="list-from">
        <el-dialog
          title="上报水电费用"
          :visible.sync="dialogFormVisible"
          width="30%"
        >
          <el-form
            :model="numberValidateForm"
            ref="numberValidateForm"
            label-width="100px"
            class="demo-ruleForm"
          >
            <el-form-item
              label="水费"
              prop="water"
              :rules="[
                { required: true, message: '水费不能为空' },
                { type: 'number', message: '必须为数字值' },
              ]"
            >
              <el-input
                type="water"
                v-model.number="numberValidateForm.water"
                autocomplete="off"
              ></el-input>
            </el-form-item>

            <el-form-item
              label="电费"
              prop="electric"
              :rules="[
                { required: true, message: '电费不能为空' },
                { type: 'number', message: '必须为数字值' },
              ]"
            >
              <el-input
                type="electric"
                v-model.number="numberValidateForm.electric"
                autocomplete="off"
              ></el-input>
            </el-form-item>

            <el-form-item>
              <el-button
                type="primary"
                @click="submitForm('numberValidateForm')"
                >提交</el-button
              >
              <el-button @click="resetForm('numberValidateForm')"
                >重置</el-button
              >
            </el-form-item>
          </el-form>
        </el-dialog>
      </div>
      <!-- <el-alert title="成功提示的文案" type="success"> </el-alert> -->
    </div>
  </div>
</template>

<script>
export default {
  props: {
    toTableData: Array, //传入数据
    tableHeader: Array, //表格肉和显示字段名
    deleteEdit: Object, //判断显示操作
    // activeColor: {
    //   type: String,
    //   default: "red",
    // },
  },
  data() {
    return {
      search: {
        title: "",
      },
      upTotal: 0, //总条数
      upPageSize: 8, //每页数据条数
      currentPage: 1, //默认显示页数
      tableData: this.toTableData, //显示数据总量
      UpShowTableData: [], //显示数据
      dialogFormVisible: false, //弹出框form表单
      formLabelWidth: "100px", //弹出框框度
      numberValidateForm: {
        //弹出框绑定数据
        water: "",
        electric: "",
      },
    };
  },
  created() {
    // this.tableData = this.toTableData;
    this.totalNum();
    this.dataShow(this.upPageSize);
    // console.log("cre");
    // console.log(this.tableData);
    // console.log(this.tableHeader);
  },

  watch: {
    toTableData(newVal) {
      this.tableData = newVal; //对父组件传过来的值进行监听，如果改变也对子组件内部的值进行改变
      // console.log(newVal);
      // console.log(this.tableData);
      this.totalNum();
      this.dataShow(this.upPageSize);
    },
  },
  mounted() {
    // this.tableData = this.toTableData;
  },
  methods: {
    //计算总条数
    totalNum() {
      this.upTotal = this.tableData.length;
    },
    //显示条数
    dataShow(num) {
      this.UpShowTableData = this.tableData.slice(0, num);
      this.upPageSize = num;
    },
    // 每页数量，传递
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.dataShow(val);
    },
    // 每页数据，第几页
    handleCurrentChange(val) {
      console.log(`当前页 ${val} 条`);
      //设置开始
      let start = this.upPageSize * val - this.upPageSize;
      // 设置结束长度
      let end = this.upPageSize * val; //长度判断
      console.log(end);
      end = end > this.tableData.length ? this.tableData.length : end;
      this.UpShowTableData = this.tableData.slice(start, end);
    },
    // 传递父组件编辑按钮
    handleEdit(index, row) {
      // this.dialogFormVisible = true;
      this.$emit("edit", row);
      console.log(index, row);
    },
    //传递父组件删除按钮
    handleEditDelete(index, row) {
      console.log(index, row);
      this.$emit("delete", row);
    },
    // 提交 弹出框
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // alert("submit!");
          console.log(this.numberValidateForm.water);
          console.log(this.numberValidateForm.electric);
          this.dialogFormVisible = false;
          setTimeout(() => {
            this.resetForm(formName);
          }, 1000);
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    // 弹出框
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
  },
};
</script>

<style lang="scss">
@import "@/assets/styles/global.scss";
.table-list {
  .list {
    box-sizing: border-box;
    // margin: 20px;
    width: 100%;
    // height: 100%;
    padding: 20px;
    .list-start {
      width: 100%;
      // height: 120px;
      background-color: #ffffff;
      color: #000;
      margin-bottom: 10px;
      padding: 10px 10px;
      box-sizing: border-box;
      .btn {
        margin-left: 10px;
      }
      // @include center;
    }
    .list-header {
      // height: 50px;
      // border: 1px solid #ccc;
      // background-color: #161212;
      // padding: 0 20px;
      // box-sizing: border-box;
    }
    .list-table {
      // height: 100%;
      background-color: #ffffff;
      padding: 0 10px;
      box-sizing: border-box;
      // overflow: scroll;
      // overflow-y: scroll;
      .pagination {
        padding-top: 10px;
        padding-bottom: 100px;
        display: flex;
        @include right;
        background-color: #ffffff;
      }
    }
  }
}
</style>
