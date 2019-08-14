<template>
  <el-card class="box">
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 输入框 -->
    <el-row>
      <el-col>
        <el-input
          @clear="getalldata()"
          clearable
          placeholder="请输入内容"
          v-model="query"
          class="search"
        >
          <el-button @click="searchUser()" slot="append" icon="el-icon-search"></el-button>
        </el-input>
        <!-- 添加按钮 -->
        <el-button type="success" @click="handeruseradd()">添加用户</el-button>
      </el-col>
    </el-row>
    <el-table :data="list" style="width: 100%" height="350px">
      <el-table-column prop="id" label="#" width="80"></el-table-column>
      <el-table-column prop="username" label="姓名" width="120"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="140"></el-table-column>
      <el-table-column prop="mobile" label="电话" width="140"></el-table-column>
      <el-table-column prop="create_time" label="创建日期" width="140">
        <!-- slot-scope自动调用外层(list)的数据 scope相当于list scope.row自动调用对象里面的数据-->
        <template slot-scope="scope">{{scope.row.create_time | filter }}</template>
      </el-table-column>
      <el-table-column label="用户状态" width="140">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.mg_state"
            @change="stateschange(scope.row)"
            active-color="#13ce66"
            inactive-color="#ff4949"
          ></el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="200">
        <template slot-scope="scope">
          <el-row>
            <el-button
              type="primary"
              @click="showEditUser(scope.row)"
              icon="el-icon-edit"
              circle
              size="mini"
              plain
            ></el-button>
            <el-button type="success" icon="el-icon-check" circle size="mini" plain></el-button>
            <el-button
              type="danger"
              @click="opendelete(scope.row)"
              icon="el-icon-delete"
              circle
              size="mini"
              plain
            ></el-button>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      class="pagefoot"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagenum"
      :page-sizes="[2, 4, 6, 8]"
      :page-size="2"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
    ></el-pagination>
    <!-- 添加用户的对话框 -->
    <el-dialog title="添加用户" :visible.sync="dialogFormVisibleAdd">
      <el-form label-position="left" label-width="80px" :model="formdata">
        <el-form-item label="用户名">
          <el-input v-model="formdata.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="formdata.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input v-model="formdata.email"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input v-model="formdata.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleAdd = false">取 消</el-button>
        <el-button type="primary" @click="usersadd()">确 定</el-button>
      </div>
    </el-dialog>
    <!--编辑用户对话框  -->
    <el-dialog title="编辑用户" :visible.sync="dialogFormVisibleEdit">
      <el-form label-position="left" label-width="80px" :model="formdata">
        <el-form-item label="用户名">
          <el-input disabled v-model="formdata.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="formdata.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input v-model="formdata.email"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input v-model="formdata.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleEdit = false">取 消</el-button>
        <el-button type="primary" @click="usersedit()">确 定</el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      pagenum: 1,
      pagesize: 2,
      total: -1,
      list: [],
      dialogFormVisibleAdd: false,
      dialogFormVisibleEdit: false,
      formdata: {
        username: "",
        password: "",
        email: "",
        mobile: ""
      }
    };
  },
  created() {
    // console.log(this);
    this.getTableData();
  },

  methods: {
    // 修改用户状态
    async stateschange(user) {
      const res = await this.$http.put(
        `users/${user.id}/state/${user.mg_state}`
      );
      console.log(res);
    },
    // 编辑用户信息链接
    async usersedit() {
      const res = await this.$http.put(
        `users/${this.formdata.id}`,
        this.formdata
      );
      const {
        meta: { msg, status }
      } = res.data;
      if (status === 200) {
        // 对话框隐藏
        this.dialogFormVisibleEdit = false;
        this.getTableData();
      }
    },
    // 编辑对话框
    showEditUser(user) {
      this.dialogFormVisibleEdit = true;
      this.formdata = user;
    },
    // 删除提示框
    opendelete(user) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await this.$http.delete(`users/${user.id}`);

          const {
            meta: { msg, status }
          } = res.data;
          if (status === 200) {
            this.$message.success(msg);
            this.getTableData();
            this.pagenum = 1;
          }
        })
        .catch(() => {
          this.$message.info("删除取消");
        });
    },
    // 用户添加
    async usersadd() {
      const res = await this.$http.post(`users`, this.formdata);
      // console.log(res);
      const {
        meta: { msg, status }
      } = res.data;
      if (status === 201) {
        this.dialogFormVisibleAdd = false;
        this.getTableData();
      }
    },
    // 头部用户添加
    handeruseradd() {
      this.dialogFormVisibleAdd = true;
      this.formdata = {};
    },
    // 清空搜索框是显示所有数据
    getalldata() {
      this.getTableData();
    },
    // 搜索功能
    searchUser() {
      this.pagenum = 1;
      // 按照query关键字搜索
      this.getTableData();
    },
    // 分页效果
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.pagenum = 1;
      this.pagesize = val;
      this.getTableData();
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.pagenum = val;
      this.getTableData();
    },
    // 用户列表数据渲染
    async getTableData() {
      const AUTH_TOKEN = localStorage.getItem("token");
      this.$http.defaults.headers.common["Authorization"] = AUTH_TOKEN;

      const res = await this.$http.get(
        `users?query=${this.query}&pagenum=${this.pagenum}&pagesize=${
          this.pagesize
        }`
      );
      // console.log(res);
      const {
        data,
        meta: { msg, status }
      } = res.data;

      if (status === 200) {
        this.total = data.total;
        this.list = data.users;
      }
    }
  }
};
</script>

<style>
.box {
  height: 100%;
}
.search {
  width: 350px;
  margin-top: 20px;
}
.pagefoot {
  margin-top: 20px;
}
</style>
