<template>
	<div class="content-page">
		<div class="content-nav">
			<el-breadcrumb class="breadcrumb" separator="/">
				<el-breadcrumb-item :to="{ path: '/dashboard' }">首页</el-breadcrumb-item>
				<el-breadcrumb-item>店铺运营</el-breadcrumb-item>
				<el-breadcrumb-item>专题管理</el-breadcrumb-item>
			</el-breadcrumb>
			<div class="operation-nav">
				<router-link to="/dashboard/operate/topic/add">
					<el-button type="primary" icon="plus">添加专题</el-button>
				</router-link>
			</div>
		</div>
		<div class="content-main">
			<div class="filter-box">
				<el-form :inline="true" :model="filterForm" class="demo-form-inline">
					<el-form-item label="专题名称">
						<el-input v-model="filterForm.name" placeholder="专题名称"></el-input>
					</el-form-item>
					<el-form-item>
						<el-button type="primary" @click="onSubmitFilter">查询</el-button>
					</el-form-item>
				</el-form>
			</div>
			<div class="form-table-box">
				<el-table :data="tableData" style="width: 100%" border stripe>
					<el-table-column prop="id" label="ID" width="100">
					</el-table-column>
					<el-table-column prop="title" label="专题名称">
					</el-table-column>
					<el-table-column prop="scene_pic_url" label="封面" width="150">
						<template scope="scope">
							<img v-if="scope.row.scene_pic_url" :src="scope.row.scene_pic_url" class="image-show">
						</template>
					</el-table-column>
					<el-table-column prop="price_info" label="价格" width="100">
					</el-table-column>
					<el-table-column prop="is_show" label="是否显示" width="100">
						<template scope="scope">
							{{ scope.row.is_show == 1 ? '是' : '否' }}
						</template>
					</el-table-column>
					<el-table-column prop="sort_order" label="排序" width="80">
					</el-table-column>
					<el-table-column label="操作" width="140">
						<template scope="scope">
							<el-button size="small" @click="handleRowEdit(scope.$index, scope.row)">编辑</el-button>
							<el-button size="small" v-if="isShowDelete" type="danger" @click="handleRowDelete(scope.$index, scope.row)">删除</el-button>
						</template>
					</el-table-column>
				</el-table>
			</div>
			<div class="page-box">
				<el-pagination @current-change="handlePageChange" :current-page="page" :page-size="10" layout="total, prev, pager, next, jumper" :total="total">
				</el-pagination>
			</div>
		</div>
	</div>
</template>

<script>

import api from "@/config/api";
export default {
	data() {
		return {
			isShowDelete : false,
			page: 1,
			total: 0,
			filterForm: {
				name: ''
			},
			tableData: []
		}
	},
	methods: {
		handlePageChange(val) {
			this.page = val;
			//保存到localStorage
			localStorage.setItem('topicPage', this.page)
			localStorage.setItem('topicFilterForm', JSON.stringify(this.filterForm));
			this.getList()
		},
		handleRowEdit(index, row) {
			this.$router.push({ name: 'topic_add', query: { id: row.id } })
		},
		handleRowDelete(index, row) {

			this.$confirm('确定要删除?', '提示', {
				confirmButtonText: '确定',
				cancelButtonText: '取消',
				type: 'warning'
			}).then(() => {

				this.axios.post('topic/destory', { id: row.id }).then((response) => {
					console.log(response.data)
					if (response.data.errno === 0) {
						this.$message({
							type: 'success',
							message: '删除成功!'
						});

						this.getList();
					}
				})


			});
		},
		onSubmitFilter() {
			this.page = 1
			this.getList()
		},
		getList() {
			this.axios.get('topic', {
				params: {
					page: this.page,
					name: this.filterForm.name
				}
			}).then((response) => {
                this.tableData = response.data.data.data
                this.page = response.data.data.currentPage
                this.total = response.data.data.count
			})
		}
	},
	components: {

	},
	mounted() {
		 this.isShowDelete = api.isShowDelete;
		this.getList();
	}
}

</script>

<style>

.image-show {
  width: 120px;
  height: 68px;
  display: block;
  float: left;
}

</style>
