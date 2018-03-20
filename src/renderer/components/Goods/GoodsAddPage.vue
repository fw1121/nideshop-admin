<template>
  <div class="content-page">

    <div class="content-nav">
      <el-breadcrumb class="breadcrumb" separator="/">
        <el-breadcrumb-item :to="{ name: 'dashboard' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>商品管理</el-breadcrumb-item>
        <el-breadcrumb-item>{{infoForm.id ? '编辑商品' : '添加商品'}}</el-breadcrumb-item>
      </el-breadcrumb>
      <div class="operation-nav">
        <el-button type="primary" @click="goBackPage" icon="arrow-left">返回列表</el-button>
      </div>
    </div>

    <div class="content-main">
      <div class="form-table-box">
        <el-form ref="infoForm" :rules="infoRules" :model="infoForm" label-width="120px">
          
          <el-form-item label="商品名称" prop="name">
            <el-input v-model="infoForm.name"></el-input>
          </el-form-item>

          <el-form-item label="推荐类型">
            <el-radio-group v-model="infoForm.stock_type">
              <el-radio-button label="0" :key="0">Amazon海淘直达</el-radio-button>
              <el-radio-button label="1" :key="1">海外产地直达</el-radio-button>
          </el-radio-group>
          </el-form-item>

          <el-form-item label="所属分类">
            <el-cascader :options="categoryOptions" :props="categoryCascaderConfig" placeholder="请选择分类" v-model="categorySelected" @change="handleChange" >
            </el-cascader>
          </el-form-item>
          <!-- <el-form-item label="所属分类" prop="name">
            <el-select v-model="infoForm.category_id" placeholder="请选择所属分类">
                <el-option v-for="item in categoryOptions" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item> -->

          <el-form-item label="所属品牌" prop="brand_id" :rules="[
          { type:'number', required: true,  message: '请选择所属品牌'}]">
            <el-select v-model="infoForm.brand_id" placeholder="请选择品牌">
              <el-option v-for="item in brandOptions" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item>
          <!-- <el-form-item label="商品简介" prop="goods_desc">
            <el-input type="textarea" v-model="infoForm.goods_desc" :rows="3"></el-input>
            <div class="form-tip"></div>
          </el-form-item> -->

          <el-form-item label="商品封面" prop="list_pic_url">
            <el-upload class="image-uploader" name="brand_pic"
                       :action="actionGoodsPic" :show-file-list="true"
                       :on-success="handleUploadImageSuccess" :headers="uploaderHeader" :data="{index:0,type:'poster'}">
              <img v-if="infoForm.list_pic_url" :src="infoForm.list_pic_url" class="image-show">
              <i v-else class="el-icon-plus image-uploader-icon"></i>
              <div class="form-tip">图片尺寸：750*420</div>
            </el-upload>
          </el-form-item>



          <el-form-item label="价格" prop="retail_price" :rules="[{ type:'number', required: true,  message: '请填写价格', trigger:'blur'}]">
            <el-input type='number' v-model.number="infoForm.retail_price"></el-input>
          </el-form-item>

          <el-form-item label="规格/库存" prop="goods_number" :rules="[{ type:'number', required: true,  message: '请填写库存', trigger:'blur'}]">
            <el-input-number v-model.number="infoForm.goods_number" :min='0' :max='99999'></el-input-number>
          </el-form-item>

          <el-form-item label="推荐类型">
            <!-- <el-checkbox-group v-model="recommendCheckedList">
              <el-checkbox label="新品" v-model="infoForm.is_new"></el-checkbox>
              <el-checkbox label="人气" v-model="infoForm.is_hot"></el-checkbox>
            </el-checkbox-group> -->
            <el-radio-group v-model="recommendChecked">
              <el-radio-button label="0" :key="1">无</el-radio-button>
              <el-radio-button label="1" :key="2">新品</el-radio-button>
              <el-radio-button label="2" :key="3">人气</el-radio-button>
          </el-radio-group>

          </el-form-item>
          <el-form-item label="上架">
            <el-switch on-text="" off-text="" v-model="infoForm.is_on_sale"></el-switch>
          </el-form-item>
          <el-form-item label="排序">
            <el-input-number v-model="infoForm.sort_order" :min="1" :max="1000"></el-input-number>
          </el-form-item>


          <el-form-item label="商品详情">
              <div v-for="(item, curIndex) in infoForm.goods_desc" :key="item">
                <el-upload  class="image-uploader" name="brand_pic"
                            :action="actionGoodsPic" :show-file-list="true"
                            :on-success="handleUploadImageSuccess" :headers="uploaderHeader" :data="{index:curIndex,type:'desc'}">
                    <img v-if="item" :src="item" class="image-show">
                    <i v-else class="el-icon-plus image-uploader-icon"></i>
                </el-upload>                
                <el-button class='image-delete' size="small" type="danger" @click="handleDeleteImg('desc', curIndex)">删除</el-button>    
              </div>

            <el-upload class="image-uploader" name="brand_pic"
                        :action="actionGoodsPic" :show-file-list="true"
                        :on-success="handleUploadImageSuccess" :headers="uploaderHeader" :data="{index:infoForm.goods_desc.length,type:'desc'}">
                <i class="el-icon-plus image-uploader-icon"></i>
            </el-upload>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="onSubmitInfo">确定保存</el-button>
            <el-button @click="goBackPage">取消</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
    
  </div>
</template>

<script>
import api from "@/config/api";
import { log } from 'util';
export default {
  data() {
    return {
      uploaderHeader: {
        "X-Nideshop-Token": localStorage.getItem("token") || ""
      },

      categoryOptions:[], // 分类数据
        categoryCascaderConfig: {
          value: 'id',
          label: 'name',
          children: 'children'
        }, // 分类数据配置
        categorySelected:[], // 默认选中的分类
        
        brandOptions:[], // 品牌数据

      recommendChecked:0, // 推荐选中数据

      actionGoodsPic : api.rootUrl+'/upload/brandPic',
      testData:{testkey:"123"},

      infoForm: {
        id: 0,
        
        name: "",
        list_pic_url: "",
        goods_desc: [],
        pic_url: "",
        sort_order: 100,
        is_show: true,
        floor_price: 0,
        is_new: false,
        new_pic_url: "",
        new_sort_order: 10,

        goods_number:0,
        brand_id : null,
        category_id:0,
        is_on_sale:true,
        is_hot:false,
        stock_type:0,
        retail_price:0,
      },
      infoRules: {
        name: [{ required: true, message: "请输入名称", trigger: "blur" }],
        brand:[{ required: true, type:"number", message: "请选择品牌", trigger: "blur" }],
        goods_desc: [
          { required: false, message: "请输入简介", trigger: "blur" }
        ],
        list_pic_url: [
          { type:"string", required: true, message: "请选择商品图片", trigger: "blur", transform(value) {
            return value;
            } }
        ],
        goods_number_rule: [
          { type:'number', required: true,  message: "请填写库存", trigger:"blur"}
        ]
      }
    };
  },
  methods: {
    goBackPage() {
      this.$router.go(-1);
    },

    handleChange(item)
    {
        this.infoForm.category_id = item[item.length-1];
    },
    onSubmitInfo() {

      if(this.recommendChecked==1)
      {
        this.infoForm.is_new = true;
        this.infoForm.is_hot = false;
      }
      else if(this.recommendChecked==2)
      {
        this.infoForm.is_new = false;
        this.infoForm.is_hot = true;
      }
      else
      {
        this.infoForm.is_new = false;
        this.infoForm.is_hot = false;
      }

      this.$refs["infoForm"].validate(valid => {
        if (valid) {
          this.axios.post("goods/store", this.infoForm).then(response => {
            if (response.data.errno === 0) {
              this.$message({
                type: "success",
                message: "保存成功"
              });
              this.$router.go(-1);
            } else {
              this.$message({
                type: "error",
                message: "保存失败"
              });
            }
          });
        } else {
          return false;
        }
      });
    },
    handleUploadImageSuccess(res, file, param1, param2) {
      console.log("res:" + res + ",file:" + file + ",param1:" + param1 + ",param2:" + param2);
      if (res.errno === 0) {
        switch (res.data.params.type) {
          //商品封面
          case "poster":
          //  this.$set("infoForm.list_pic_url", res.data.fileUrl);
            this.infoForm.list_pic_url = res.data.fileUrl;

            break;
          case "brand_new_pic":
            this.$set("infoForm.new_pic_url", res.data.fileUrl);
            break;

          // 商品描述图片
          case "desc":

            let index = parseInt(res.data.params.index);
            if(index>=this.infoForm.goods_desc.length) // 新增图片
            {
              this.infoForm.goods_desc.push(res.data.fileUrl);
            }
            else // 替换图片
            {
               this.infoForm.goods_desc.splice(index,1,res.data.fileUrl);
            }
            
            break;
        }
      }
    },

    /** 
     *  处理删除图片
     * @param type 类型 desc、poster、gallery
     */
    handleDeleteImg(type, index)
    {
        this.axios.post("goods/store", this.infoForm).then(response => {
            if (response.data.errno === 0) {
              this.$message({
                type: "success",
                message: "保存成功"
              });
              this.$router.go(-1);
            } else {
              this.$message({
                type: "error",
                message: "保存失败"
              });
            }
          });
    },

    getCascaderCategory() {
        this.axios.get('category/cascader').then((response) => {
          this.categoryOptions = this.categoryOptions.concat(response.data.data);
          this.handleCategorySelected();
        });
      },

    handleCategorySelected()
    {
        if(this.categoryOptions.length>0 && this.infoForm.category_id >0)
        {
          this.categoryOptions.map((item) => {
            item.children.map((itemChild)=>{
              if (itemChild.id === this.infoForm.category_id) {
                this.categorySelected = [item.id, this.infoForm.category_id];
                return;
              }
            });
          });
        }
    },

    getBrand()
    {
        this.axios.get('brand', {params: {"size": 500}}).then((response) => {
          this.brandOptions = this.brandOptions.concat(response.data.data.data);
          this.handleCategorySelected();
        });
    },

    getInfo() {
      if (this.infoForm.id <= 0) {
        return false;
      }

      //加载商品详情
      let that = this;
      this.axios
        .get("goods/info", {
          params: {
            id: that.infoForm.id
          }
        })
        .then(response => {
          let resInfo = response.data.data;
          resInfo.is_new = resInfo.is_new ? true : false;
          resInfo.is_hot = resInfo.is_hot ? true : false;
          resInfo.is_show = resInfo.is_show ? true : false;
          resInfo.is_on_sale = resInfo.is_on_sale ? true : false;
          resInfo.brand_id = resInfo.brand_id==0 ? null : resInfo.brand_id;

          if(resInfo.is_new)
          {
            this.recommendChecked = 1;
          } 
          else if(resInfo.is_hot)
          {
            this.recommendChecked = 2;
          }
          else
          {
            this.recommendChecked = 0;
          }

          that.infoForm = resInfo;

          this.handleCategorySelected();
        });
    }
  },
  components: {},
  mounted() {
    this.getCascaderCategory();
    this.getBrand();
    this.infoForm.id = this.$route.query.id || 0;
    this.getInfo();
    console.log(api);
  }
};
</script>

<style>
.image-uploader {
  height: 130px;
  display: inline-block;
}
.image-uploader .el-upload {
  border: 1px solid #d9d9d9;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  
}

.image-uploader .el-upload:hover {
  border-color: #20a0ff;
}

.image-uploader .image-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 187px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}

.image-uploader .image-show {
  width: 187px;
  height: 100px;
  display: block;
  float: left;
}

.image-delete
{
  overflow: hidden;
  bottom:10px;
}

.image-uploader.new-image-uploader {
  font-size: 28px;
  color: #8c939d;
  width: 165px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}

.image-uploader.new-image-uploader .image-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 165px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}

.image-uploader.new-image-uploader .image-show {
  width: 165px;
  height: 100px;
  display: block;
}


</style>
