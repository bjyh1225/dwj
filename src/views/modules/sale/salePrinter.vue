<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>打印机管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="addHandle()">新增</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100" @change="industryChange">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.sellerCode" size="mini" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="item in sellers" :key="item.value" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-button @click="searchHndel()" type="primary" size="mini">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button @click="searchReset" size="mini" type="primary">重置</el-button>
				</el-form-item>
			</el-form>
		</div>
		<el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" ref="multipleTable" style="width: 100%;">
			<el-table-column type="selection" header-align="center" align="center" width="50">
			</el-table-column>
			<el-table-column prop="printTypeNo" header-align="center" align="center" label="序号">
			</el-table-column>
			<el-table-column prop="uuid" header-align="center" align="center" label="UUID">
			</el-table-column>
			<el-table-column prop="sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="fullName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="printerPlatformType" header-align="center" align="center" label="打印平台类型">
			</el-table-column>
			<el-table-column prop="printerModel" header-align="center" align="center" label="打印机型号">
			</el-table-column>
			<el-table-column prop="printerWidthType" header-align="center" align="center" label="打印宽度">
			</el-table-column>
			<el-table-column prop="printerCode" header-align="center" align="center" label="打印机编码">
			</el-table-column>
			<el-table-column prop="terminalCode" header-align="center" align="center" label="终端编号">
			</el-table-column>
			<el-table-column prop="terminalSecretKey" header-align="center" align="center" label="终端密钥">
			</el-table-column>
			<el-table-column prop="lineStatus" header-align="center" align="center" label="联网状态">
			</el-table-column>
			<el-table-column prop="lastOnlineTime" header-align="center" align="center" label="最后在线">
			</el-table-column>
			<el-table-column prop="status" header-align="center" align="center" label="状态">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>
			<el-table-column prop="createUserTpye" header-align="center" align="center" label="创建人">
			</el-table-column>
			<el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateUserType" header-align="center" align="center" label="更新人">
			</el-table-column>
			<el-table-column prop="updateTime" header-align="center" align="center" label="更新时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
	
		<el-dialog title="新增" :close-on-click-modal="false" :visible.sync="addvisible" width="760px">
			<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="160px" class="w-80">
				<el-form-item label="商家名称" prop="sellerCode">
					<el-select v-model="dataForm.sellerCode" clearable placeholder="商家名称" clearable class="w-100">
						<el-option v-for="(item,index) in allsellers" :key="index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<!--<el-form-item label="打印机平台类型" prop="printerPlatformType">
					<el-select v-model="dataForm.printerPlatformType" clearable placeholder="打印机平台类型" clearable class="w-100">
						<el-option v-for="(item,index) in allTypes" :key="index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="打印机型号" prop="printerModel">
					<el-select v-model="dataForm.printerPlatformType" clearable placeholder="打印机型号" clearable class="w-100">
						<el-option v-for="(item,index) in allTypes" :key="index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="打印宽度" prop="printerWidthType">
					<el-select v-model="dataForm.printerPlatformType" clearable placeholder="打印宽度" clearable class="w-100">
						<el-option v-for="(item,index) in allTypes" :key="index" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>-->
				<el-form-item label="打印机编码" prop="printerCode">
					<el-input v-model="dataForm.printerCode" placeholder="打印机编码"></el-input>
				</el-form-item>
				<el-form-item label="打印ip地址" prop="printerIp">
					<el-input v-model="dataForm.printerIp" placeholder="打印ip地址"></el-input>
				</el-form-item>
				<el-form-item label="打印机端口号" prop="printerPort">
					<el-input v-model="dataForm.printerPort" placeholder="打印机端口号"></el-input>
				</el-form-item>
				<el-form-item label="终端编号" prop="terminalCode">
					<el-input v-model="dataForm.terminalCode" placeholder="终端编号"></el-input>
				</el-form-item>
				<el-form-item label="终端秘钥" prop="terminalSecretKey">
					<el-input v-model="dataForm.terminalSecretKey" placeholder="终端秘钥"></el-input>
				</el-form-item>
				<el-form-item label="终端名称" prop="terminalName">
					<el-input v-model="dataForm.terminalName" placeholder="终端名称"></el-input>
				</el-form-item>
				<el-form-item label="终端手机号" prop="terminalPhone">
					<el-input v-model="dataForm.terminalPhone" placeholder="终端手机号"></el-input>
				</el-form-item>
				<el-form-item label="状态" prop="status">
					<el-radio-group v-model="dataForm.status">
						<el-radio :label="0">启用</el-radio>
						<el-radio :label="1">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
				<el-form-item label="备注" prop="note">
					<el-input v-model="dataForm.note" placeholder="备注"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="addvisible = false">取消</el-button>
        		<el-button type="primary" @click="addDataFormSubmit()">确定</el-button>
      		</span>
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataList: [],
				condition: {
					industryTypeCode: '',
					sellerCode: '',
					page: 1,
					size: 10
				},
				dataForm: {
					sellerCode: '',
					printerPlatformType: '',
					printerModel: '',
					printerWidthType: '',
					printerCode: '',
					printerIp: '',
					printerPort: '',
					terminalCode: '',
					terminalSecretKey: '',
					terminalName: '',
					terminalPhone: '',
					status: '',
					note: ''
				},
				dataRule: {},
				types: [],
				sellers: [],
				allsellers: [],
				allTypes: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				addvisible:false
			};
		},
		activated() {
			this.getDataList()
			this.getTypes()
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			//获取行业列别
			getTypes() {
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: 'industry'
					})
				}).then(({
					data
				}) => {
					this.types = data.dataList
				});
			},
			
			industryChange(val) {
				this.condition.sellerCode = ''
				//获取商家
				this.$http({
					url: this.$http.adornUrl("/sale/saleSeller/findByIndustryTypeCode"),
					method: "post",
					params: this.$http.adornParams({
						industryTypeCode: val
					})
				}).then(({
					data
				}) => {
					this.sellers = data.data
				});				
			},

			searchReset() {
				this.condition = {
					industryTypeCode: '',
					sellerCode: '',
					page: 1,
					size: 10
				}
			},
			
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				this.$http({
					url: this.$http.adornUrl("/sale/salePrinter/list"),
					method: "post",
					data: this.$http.adornData({
						page: this.pageIndex,
						size: this.pageSize,
						condition: this.condition
					})
				}).then(({
					data
				}) => {
					if(data && data.code === 0) {
						this.dataList = data.page.list;
						this.totalPage = data.page.totalCount;
					} else {
						this.dataList = [];
						this.totalPage = 0;
					}
					this.dataListLoading = false;
				});
			},
			indexMethod(index) {
				return(this.currentPage - 1) * this.pageSize + index + 1
			},

			// 每页数
			sizeChangeHandle(val) {
				this.pageSize = val;
				this.pageIndex = 1;
				this.getDataList();
			},
			// 当前页
			currentChangeHandle(val) {
				this.pageIndex = val;
				this.getDataList();
			},
			// 多选
			selectionChangeHandle(val) {
				this.dataListSelections = val;
			},
			addHandle() {
				this.dataForm = {
					sellerCode: '',
					printerPlatformType: '',
					printerModel: '',
					printerWidthType: '',
					printerCode: '',
					printerIp: '',
					printerPort: '',
					terminalCode: '',
					terminalSecretKey: '',
					terminalName: '',
					terminalPhone: '',
					status: '',
					note: ''
				}
				this.addvisible = true
			}
		}
	};
</script>

<style scoped>

</style>