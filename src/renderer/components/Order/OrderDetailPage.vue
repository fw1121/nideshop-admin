<template>
    <div class="content-page">
        <div class="content-nav">
            <div class="breadcrumb">
                订单详情
            </div>
            <div class="operation-nav">
                <el-button type="primary" @click="goBackPage" size="small" icon="arrow-left">返回列表</el-button>
            </div>
        </div>
        <div class="content-main">
            <div class="form-table-box" v-loading="pageLoading" element-loading-text="拼命加载中">
               
               <el-form ref="infoForm" :rules="infoRules" :model="infoForm" label-width="120px">

                    <el-form-item prop="deletedPics">
                    </el-form-item>

                    <el-form-item label="订单号" prop="order_sn">
                        {{infoForm.order_sn}}
                    </el-form-item>
                    <el-form-item label="订单状态" prop="order_status">
                        {{infoForm.order_status}}
                        <el-button type="primary" @click="onSubmitInfo">修改状态</el-button>
                    </el-form-item>

                    <el-form-item label="订单价格" prop="order_price">
                        ¥{{infoForm.actual_price}}
                    </el-form-item>

                    <el-form-item label="下单时间" prop="add_time">
                        {{infoForm.add_time}}
                    </el-form-item>
                    
                    <el-form-item label="收货人" prop="consignee">
                        {{infoForm.consignee}}
                    </el-form-item>
                    <el-form-item label="收货地址">
                    </el-form-item>

                    <el-form-item label="联系方式" prop="mobile">
                         {{infoForm.mobile}}
                    </el-form-item>

                    <el-form-item label="买家留言" prop="postscript">
                        {{infoForm.postscript}}
                    </el-form-item>

                    <el-form-item label="商品详情">
                        
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
    export default {
        data() {
            return {
                pageLoading: false,
                infoForm: {
                    id: 0,
                    order_sn: 0,
                    order_status:0,
                    order_price:0,
                    add_time:0,
                    consignee:'',
                    mobile:'',
                    postscript:'',
                }
            }
        },
        methods: {
            goBackPage() {
                this.$router.go(-1);
            },
            getInfo() {
                if (this.infoForm.id <= 0) {
                    return false
                }

                //加载订单详情
                let that = this
                this.axios.get('order/info', {
                    params: {
                        id: that.infoForm.id
                    }
                }).then((response) => {
                    console.log(response.data);
                    let resInfo = response.data.data;
                    that.infoForm = resInfo;
                    this.pageLoading = false;
                })
            },

            /** 修改订单状态 */
            modifyOrderStatus()
            {
                
            },

            /** 关闭订单 */
            closeOrder()
            {
                
            },

            onSubmitInfo()
            {

            }

        },
        components: {},
        mounted() {
            console.log(this.$route.query)
            this.infoForm.id = this.$route.query.id || 0;
            this.getInfo();
        }
    }

</script>

<style>
   
</style>
