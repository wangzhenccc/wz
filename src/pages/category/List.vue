<template>

<div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
<el-table :data="categories">
    <el-table-column prop="id" label="编号"></el-table-column>
    <el-table-column prop="name" label="栏目名称"></el-table-column>
    <el-table-column prop="num" label="序号"></el-table-column>
    <el-table-column prop="parentId" label="父目栏"></el-table-column>
    <el-table-column prop="icon" label="图标"></el-table-column>
    <el-table-column label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
            <a href="" @click.prevent="toUpdateHandler">修改</a>
        </template>
    </el-table-column>
</el-table>
 <!--分页-->
<el-pagination
    layout="prev, pager, next"
    :total="1000">
  </el-pagination>
  <!--分页结束-->
   <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="30%">
  {{form}}
  <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="序号">
          <el-input type="password" v-model="form.password"></el-input> 
        </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
<!--模态框结束-->
</div>
</template>
<script>
import querystring from 'querystring'
import request from '@/utils/request'
export default {
//用于存放网页中需要调用的方法
    methods:{
      loaddata(){
        let url = "http://localhost:6677/category/findAll"
                request.get(url).then((response)=>{
                    //将查询结果放置到customers中,“=>”保证this指向外部函数的this。
                    this.categorys=response.data;
                })
      },
      //this.form对象---字符串--->后台
      //通过request有后台进行交互，并且要携带参数
        submitHandler(){
            let url="http://localhost:6677/category/saveOrUpdate";
            request({
              url,
              method:"POST",
              headers:{
                "Content-Type":"application/x-www-form-urlencoded"
              },
              data:querystring.stringify(this.form)

            }).then((response)=>{
            //请求结束
            this.closeModalHandler();
            this.loaddata();
            this.$message({
              type:"success",
              message:response.message
            }

            )
            })
        },
        toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!' +id
          });
        })
        },
        toUpdateHandler(){
            this.title="修改栏目信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },    
        toAddHandler(){
            this.visible=true;
            this.title="序号";
        }
    },
//用于存放要向网页中显示的信息
    data(){
        return{
            visible:false,
            category:[],
            form:{
              type:"category"
            }
            }
    },
    created(){
        //vue实例创建完毕
        this.loaddata();
    }
}
</script>
<style scoped>

</style>