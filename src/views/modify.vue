<template>
    <div>
         <!-- 头部 -->
         <div class="add_head">
             <el-button type="text" @click="goback"><i class="el-icon-back"></i></el-button>
             <span class="p_title">添加商品</span>
         </div>
           <!-- 中间表单 -->
     <el-card shadow="never" style="padding:20px;">
             <!-- 表单 -->
             <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
            <!-- 商品名称 -->
            <el-form-item label="商品名称" prop="name">
                    <el-input v-model="ruleForm.name" placeholder="请输入商品名称"></el-input>
            </el-form-item>
            <!-- 商品描述 -->
            <el-form-item label="商品描述" prop="desc">
                <el-input type="textarea" v-model="ruleForm.desc" placeholder="请输入商品描述"></el-input>
            </el-form-item>
            <!-- 商品价格 -->
            <el-form-item label="商品价格" prop="price">
                <el-input placeholder="请输入商品价格" v-model="ruleForm.price">
                    <template slot="append">元</template>
                </el-input>
            </el-form-item>
            <!-- 商品分类 -->
              <el-form-item label="商品分类" style="text-align:left" prop="categoryId">
                <el-select v-model="ruleForm.categoryId" >
                  <el-option v-for=" n in liststyle" :label="n.name" :value="n._id" :key="n._id"></el-option>
                </el-select>
            </el-form-item>
            <!-- 上传图片 -->
            <el-form-item label="商品图片" style="text-align:left">
                <el-upload
                action="https://jsonplaceholder.typicode.com/posts/"
                    list-type="picture-card"
                    :on-preview="handlePictureCardPreview"
                    :on-remove="handleRemove">
                    <i class="el-icon-plus"></i>
                 </el-upload>
                 <el-dialog :visible.sync="dialogVisible">
                    <img width="100%" :src="dialogImageUrl">
                 </el-dialog>
            </el-form-item>
             <!--富文本编辑器-->
            <el-form-item label="商品详情" style="text-align:left;">
               
                <div class="edit_container">
                  <quill-editor
                    v-model="ruleForm.detail"
                    ref="myQuillEditor"
                    :options="editorOption"
                    @blur="onEditorBlur($event)">
                  </quill-editor>
                </div>
            </el-form-item>

            <!-- 按钮 -->
            <el-form-item style="text-align:left;">
                <el-button type="primary" @click="OnSure">确认提交</el-button>
                 <el-button @click="Onreset">重置</el-button>
            </el-form-item>
            </el-form>
            <!-- 表单结束 -->
                </el-card>

    </div>
</template>
<script>
import {getCategory,getModity} from "../api/common"
export default {
    data(){
        return{
        ruleForm: {
                _id:"",//商品ID
                categoryId:"",//分类ID
                pCategoryId:"",//父分类ID
                name: '',//商品名称
                region: '',//默认选中的项
                desc: '',//商品描述
                price:'',//商品价格
                detail:'',//商品详情
                imgs:[],//图片数组
                },
          //表单验证
        rules: {
          name: [
            { required: true, message: '请输入商品名称', trigger: 'blur' },
          ],
          desc: [
            { required: true, message: '请填写商品描述', trigger: 'blur' }
          ],
          price:[
              { required: true, message: '请填写商品价格', trigger: 'blur' }
          ]
        },
        // 上传图片
         dialogImageUrl: '',
         dialogVisible: false,
            //富文本框
           content: ``,
           // 定义富文本编辑器显示
           editorOption: {
            //   modules:{
            //       toolbar:[
            //            ['bold','italic','underline','strike'], // 加粗、倾斜、下划线、删除线
            //            [{'header':1},{'header':2}], // 标题一、标题二
            //            [{'list':'ordered'},{'list':'bullet'}], // 列表
            //       ]
            // }
           },
           liststyle:[],
           modidata:""
        }
    },
    methods: {
         // 返回
        goback(){
            if(this.$router.currentRoute.name!='goods'){
            this.$router.push("/home/goods")
          }
        },
            //   上传图片
     handleRemove(file, fileList) {

      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
         // 失去焦点事件
      onEditorBlur(){

      },
           //获取分类列表
       getStyleList(){
          getCategory().then((res)=>{
            this.liststyle=res.data.data.list
      })
      },
      OnSure(){
          getModity(this.ruleForm).then((res)=>{
              if(!res.data.status){
             this.$message({
                type: 'success',
                message: "修改成功"
          });
              }
             
          })
      },
      Onreset(){
     this.ruleForm.pCategoryId=""
     this.ruleForm.categoryId=""
     this.ruleForm._id=""
     this.ruleForm.name=""
     this.ruleForm.desc=""
     this.ruleForm.price=""
     this.ruleForm.detail=""
     this.ruleForm.imgs=""
      }


    },
     mounted(){
      this.getStyleList()
     this.modidata = JSON.parse(localStorage.getItem("modidata"))
     this.ruleForm.pCategoryId=this.modidata.categoryId
     this.ruleForm.categoryId=this.modidata.categoryId
     this.ruleForm._id=this.modidata._id
     this.ruleForm.name=this.modidata.name
     this.ruleForm.desc=this.modidata.desc
     this.ruleForm.price=this.modidata.price
     this.ruleForm.detail=this.modidata.detail
     this.ruleForm.imgs=this.modidata.imgs

    }
}
</script>
<style lang="less">
 .edit_container .ql-editor{
     min-height: 200px
   }
    
</style>