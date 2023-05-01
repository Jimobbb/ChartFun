<template lang="pug">
  //- div
  //-   el-row(:gutter="36")
  //-     el-col(:span="6" v-for="item in chartList" :key="item._id")
  //-     el-col(:span="6")
  //-       el-card(:body-style="{ padding: '0px' }" shadow="hover" @click.native="addNewModel")
  //-           .add-card
  //-             i.el-icon-circle-plus
  div
    el-row(style="margin-bottom: 20px;")
      el-button(type="primary" @click="addNewModel") 新增模型
    el-table(:data="modelList")
      el-table-column(prop="_id" label="id")
        template(slot-scope="scope")
          span {{ scope.row._id | simplifyID }}
      el-table-column(prop="name" label="名称")
      el-table-column(prop="createdAt" label="上传时间")
        template(slot-scope="scope")
          span {{ $dayjs(scope.row.createdAt).format('YYYY-MM-DD HH:mm') }}
      el-table-column(label="操作")
        template(slot-scope="scope")
          el-button(type="text" size="small" @click="renameData(scope.row)") 重命名
          el-button(type="text" size="small" @click="deleteData(scope.row._id)") 删除
</template>

<script>
export default {
  props: ['user'],
  data() {
    return {
      modelList: [],
    }
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      console.log('modsel');
      // this.$http
      //   .get('/chart?uid=' + this.user.uid)
      //   .then(res => {
      //     const { errno, data } = res.data;
      //     if (errno === 0) {
      //       this.chartList = data.chartList;
      //     }
      //   })
      //   .catch(() => {});
    },
    editModel(id) {
      console.log('idddd', `/editModel/${id}`);
      this.$router.push(`/editModel/${id}`);
    },
    addNewModel() {
      this.$prompt('输入模型名称', '创建模型', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
      })
        .then(({ value }) => {
          // this.$http
          //   .post('/chart', {
          //     title: value,
          //     uid: this.user.uid,
          //   })
          //   .then(res => {
          //     const { errno, data } = res.data;
          //     if (errno === 0) {
          //       this.$message({
          //         type: 'success',
          //         message: '创建成功'
          //       });
          //       // this.getData();
          //       this.editModel(data._id);
          //     }
          //   })
          //   .catch(() => {});
          this.editModel(value);
        })
        .catch(() => {});
    },
    renameData(row) {
      this.$prompt('输入模型名称', '重命名', {
        inputValue: row.name,
        confirmButtonText: '确定',
        cancelButtonText: '取消',
      })
        .then(({ value }) => {
          // this.$http
          //   .put(`/connect/${row._id}`, {
          //     name: value
          //   })
          //   .then(res => {
          //     const { errno, data } = res.data;
          //     if (errno === 0) {
          //       this.$message({
          //         type: 'success',
          //         message: '保存成功'
          //       });
          //       setTimeout(() => {
          //         this.getData();
          //       }, 100);
          //       // this.editChart(data._id);
          //     }
          //   })
          //   .catch(() => {});
        })
        .catch(() => {});
    },
    deleteData(id) {
      this.$confirm('是否删除当前模型？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // this.$http
        //   .delete(`/connect/${id}`)
        //   .then(res => {
        //     const { errno, data } = res.data;
        //     if (errno === 0) {
        //       this.$message({
        //         type: "success",
        //         message: "已删除"
        //       });
        //       setTimeout(() => {
        //         this.getData();
        //       }, 100);
        //       // this.editChart(data._id);
        //     }
        //   });
      }).catch(() => {});
    },
  }
};
</script>

<style lang="scss" scoped>
.page-header-slot {
  position: fixed;
  top: 56px;
  left: 240px;
  right: 0;
  padding: 0 40px 20px;
  z-index: 8;
  background-color: rgba(255, 255, 255, 0.94);

  .search {
    width: 240px;
    float: right;
  }
}

.el-card {
  margin-bottom: 36px;

  .image {
    width: 100%;
    height: 150px;
    display: block;
    opacity: 0.6;
    transition: opacity 0.3s ease;
  }

  &:hover {
    cursor: pointer;
    .image {
      opacity: 0.8;
    }
  }
}

.el-card .add-card {
  height: 200px;
  line-height: 200px;
  text-align: center;
  font-size: 82px;
  color: rgba(0, 0, 0, 0.08);
}
</style>