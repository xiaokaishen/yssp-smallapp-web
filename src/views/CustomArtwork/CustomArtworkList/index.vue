<template>
  <div class="app-container">
    <div class="filter-container">
     <el-button class="filter-item" type="primary" icon="el-icon-download"
      >导出excle
      </el-button>
    </div>
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
      style="width: 100%;">
      <el-table-column align="center" label="ID" width="50">
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="作品名称" width="150">
        <template slot-scope="scope">
          <span>{{ scope.row.customArtworkName }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="作品类别" width="150">
        <template slot-scope="scope">
          <span>{{ options[scope.row.customArtworkType-1].workTypeName  }}</span>
        </template>
      </el-table-column>

      <el-table-column align="center" label="长度（CM）" width="120">
        <template slot-scope="scope">
          <span>{{ scope.row.customArtworkHeight }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="宽度（CM）" width="120">
        <template slot-scope="scope">
          <span>{{ scope.row.customArtworkWidth }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="重量(KG)" width="120">
        <template slot-scope="scope">
          <span>{{ scope.row.customArtworkWeight }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="年份" width="100">
        <template slot-scope="scope">
          <span>{{ scope.row.customArtworkYears }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="库存" width="100">
        <template slot-scope="scope">
          <span>{{ scope.row.customArtworkStock }}</span>
        </template>
      </el-table-column>

      <el-table-column align="center" class-name="status-col" label="操作">
        <template slot-scope="scope">
          <router-link :to="'/CustomArtworkAdd?customArtworkId='+scope.row.customArtworkId">
            <el-button size="small" type="primary">编辑</el-button>
          </router-link>
          <el-button size="small"
                     @click="handleDel(scope.row)" type="danger">刪除
          </el-button>
          <router-link :to="'/customArtworkDetail?customArtworkId='+scope.row.customArtworkId">
            <el-button size="small" type="success">详情</el-button>
          </router-link>
          <router-link :to="'/toAdoptList?customArtworkId='+scope.row.customArtworkId+'&isbang='+scope.row.customArtworkArtId">
            <el-button style="background-color: #dd6161" v-if ="scope.row.customArtworkArtId != 0 && scope.row.customArtworkArtId != null" size="small" type="success">已绑定</el-button>
            <el-button style="background-color: #66b1ff" v-if ="scope.row.customArtworkArtId == 0 || scope.row.customArtworkArtId == null" size="small" type="success">绑定</el-button>
          </router-link>
        </template>
      </el-table-column>
    </el-table>

    <div class="pagination-container">
      <el-pagination :current-page="page" :page-sizes="[10,20,30, 50]" :page-size="rows" :total="total" background
                     layout="total, sizes, prev, pager, next, jumper" @size-change="handleSizeChange"
                     @current-change="handleCurrentChange"></el-pagination>
    </div>

  </div>
</template>

<script>
  import {getList,delCustomerArtWorkById,selectAllWorkType} from '@/api/customArtwork'

  export default {
    data() {
      return {
        page: 1,  //页数
        rows: 10, //每页显示行数
        total: null,
        list: null,  //数据列表
        listLoading: false, //刷新加载框
        dialogStatus: '',//头部按钮跟踪
        dialogFormVisible: false, //表单是否展示
        options: []    //艺术品类别下拉框
      }
    },
    created() {
      this.fetchData();
    },
    methods: {
      fetchData() {//页面初始化加载数据
        let self = this;
        self.listLoading = true
        getList(self.page, self.rows).then(response => {
              console.log(response);
              if (response.status == 200) {
                console.log("传来的数据")
                console.log(response.data)
                self.listLoading = false;
                self.list = response.data.rows;
              } else if (response.status == 400) {
                self.listLoading = false;
                self.list = null;
              }
        })
        selectAllWorkType().then(response => {
                  console.log("selectAllWorkType:",response.data);
                  this.options = response.data;
         })
      },
      handleDel(rows){//删除某个在售艺术品
        delCustomerArtWorkById(rows.customArtworkId).then(response => {
          console.log(response);
          if (response.status == 200) {
            this.fetchData();
            this.$notify({
              title: '成功',
              message: response.data,
              type: 'success',
              duration: 2000
            })
          }
        })
      },
      handleSizeChange(val) {
        this.rows = val
        this.fetchData()
      },
      handleCurrentChange(val) {
        this.page = val
        this.fetchData()
      },


    }
  }
</script>

<style>
</style>
