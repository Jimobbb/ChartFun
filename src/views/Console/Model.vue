<template lang="pug">
  div

    el-row(:gutter="36")
      el-col(:span="6" v-for="item in chartList" :key="item._id")
      el-col(:span="6")
        el-card(:body-style="{ padding: '0px' }" shadow="hover" @click.native="addNewModel")
            .add-card
              i.el-icon-circle-plus
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