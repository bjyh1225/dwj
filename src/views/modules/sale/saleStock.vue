<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>库存管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="linHandle()">列选择</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-cascader size="mini" v-model="condition.address" :options="AddressList" placeholder="区域" expand-trigger="hover" :clearable="true" :change-on-select="true"></el-cascader>
				</el-form-item>
				<el-form-item>
					<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100" @change="industryChange">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.platformTypeCode" size="mini" clearable placeholder="平台类别">
						<el-option v-for="item in platforms" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.sellerCode" size="mini" clearable placeholder="商家名称" clearable class="w-100" @change="sellerChange">
						<el-option v-for="item in sellers" :key="item.value" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>

				<el-form-item>
					<el-select v-model="condition.goodsTypeCode" size="mini" clearable placeholder="商品类别">
						<el-option v-for="item in goodstypes" :key="item.value" :label="item.name" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.listStatus" size="mini" clearable placeholder="上架状态">
						<el-option label="已上架" value="0"></el-option>
						<el-option label="未上架" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.goodsFullName" placeholder="商品名称" clearable size="small"></el-input>
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
			<el-table-column prop="uuid" v-if="showlist.uuid" header-align="center" align="center" label="UUID">
			</el-table-column>
			<el-table-column prop="industryTypeCode" v-if="showlist.industryTypeCode" header-align="center" align="center" label="行业类别编码">
			</el-table-column>
			<el-table-column prop="industryTypeName" v-if="showlist.industryTypeName" header-align="center" align="center" label="行业类别名称">
			</el-table-column>
			<el-table-column prop="platformTypeCode" v-if="showlist.platformTypeCode" header-align="center" align="center" label="平台商品类别编码">
			</el-table-column>
			<el-table-column prop="platformTypeName" v-if="showlist.platformTypeName" header-align="center" align="center" label="平台商品类别名称">
			</el-table-column>
			<el-table-column prop="platformTypeDiscountScope" v-if="showlist.platformTypeDiscountScope" header-align="center" align="center" label="平台商品类别折扣范围">
			</el-table-column>
			<el-table-column prop="sellerCode" v-if="showlist.sellerCode" header-align="center" align="center" label="商家编码">
			</el-table-column>
			<el-table-column prop="sellerName" v-if="showlist.sellerName" header-align="center" align="center" label="商家名称">
			</el-table-column>
			<el-table-column prop="goodsTypeCode" v-if="showlist.goodsTypeCode" header-align="center" align="center" label="商品类别编码">
			</el-table-column>
			<el-table-column prop="goodsTypeName" v-if="showlist.goodsTypeName" header-align="center" align="center" label="商品类别名称">
			</el-table-column>
			<el-table-column prop="goodsTypeDiscount" v-if="showlist.goodsTypeDiscount" header-align="center" align="center" label="商品类别折扣">
			</el-table-column>
			<el-table-column prop="goodsNo" v-if="showlist.goodsNo" header-align="center" align="center" label="序号">
			</el-table-column>
			<el-table-column prop="goodsUnit" v-if="showlist.goodsUnit" header-align="center" align="center" label="商品单位">
			</el-table-column>
			<el-table-column prop="nonMemberPrice" v-if="showlist.nonMemberPrice" header-align="center" align="center" label="商品价格">
			</el-table-column>
			<el-table-column prop="memberPrice" v-if="showlist.memberPrice" header-align="center" align="center" label="会员价">
			</el-table-column>
			<el-table-column prop="goodsLable" v-if="showlist.goodsLable" header-align="center" align="center" label="商品标签">
			</el-table-column>
			<el-table-column prop="goodsUrl" v-if="showlist.goodsUrl" header-align="center" align="center" label="商品图标url" :key="Math.random()">
				<template slot-scope="scope">
					<img :src="scope.row.goodsUrl" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="goodsFullName" v-if="showlist.goodsFullName" header-align="center" align="center" label="商品名称">
			</el-table-column>
			<el-table-column prop="goodsCode" v-if="showlist.goodsCode" header-align="center" align="center" label="商品编码">
			</el-table-column>
			<el-table-column prop="goodsDescribe" v-if="showlist.goodsDescribe" header-align="center" align="center" label="商品描述">
			</el-table-column>
			<el-table-column prop="linkStatus" v-if="showlist.linkStatus" header-align="center" align="center" label="链接状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.linkStatus == 0">不链接</span>
					<span v-else-if="scope.row.linkStatus == 1">连接</span>
				</template>
			</el-table-column>
			<el-table-column prop="goodsLinkUrl" v-if="showlist.goodsLinkUrl" header-align="center" align="center" label="商品链接">
			</el-table-column>
			<el-table-column prop="manageType" v-if="showlist.manageType" header-align="center" align="center" label="经营模式">
			</el-table-column>
			<el-table-column prop="serveTime" v-if="showlist.serveTime" header-align="center" align="center" label="营业时间">
			</el-table-column>
			<el-table-column prop="serveStatus" v-if="showlist.serveStatus" header-align="center" align="center" label="营业状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.serveStatus == 0">营业中</span>
					<span v-else-if="scope.row.serveStatus == 1">未营业</span>
				</template>
			</el-table-column>
			<el-table-column prop="billStatus" v-if="showlist.billStatus" header-align="center" align="center" label="是否提供发票" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.billStatus == 0">不提供</span>
					<span v-else-if="scope.row.billStatus == 1">提供</span>
				</template>
			</el-table-column>
			<el-table-column prop="supplyTime" v-if="showlist.supplyTime" header-align="center" align="center" label="供应时间">
			</el-table-column>
			<el-table-column prop="validTime" v-if="showlist.validTime" header-align="center" align="center" label="有效期">
			</el-table-column>
			<el-table-column prop="stockDate" v-if="showlist.stockDate" header-align="center" align="center" label="库存日期">
			</el-table-column>
			<el-table-column prop="stockCode" v-if="showlist.stockCode" header-align="center" align="center" label="库存编号">
			</el-table-column>
			<el-table-column prop="stockVersion" v-if="showlist.stockVersion" header-align="center" align="center" label="库存版本">
			</el-table-column>
			<el-table-column prop="stockRule" v-if="showlist.stockRule" header-align="center" align="center" label="库存规则" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.stockRule == 0">无库存</span>
					<span v-else-if="scope.row.stockRule == 1">总库存</span>
					<span v-else-if="scope.row.stockRule == 2">固定库存</span>
				</template>
			</el-table-column>
			<el-table-column prop="stockQuantity" v-if="showlist.stockQuantity" header-align="center" align="center" label="库存数量">
			</el-table-column>
			<el-table-column prop="nowStockQuantity" v-if="showlist.nowStockQuantity" header-align="center" align="center" label="实时库存">
			</el-table-column>
			<el-table-column prop="initialStockQuantity" v-if="showlist.initialStockQuantity" header-align="center" align="center" label="初始化库存数量">
			</el-table-column>
			<el-table-column prop="stockAdjust" v-if="showlist.stockAdjust" header-align="center" align="center" label="库存调整">
			</el-table-column>
			<el-table-column v-if="showlist.stockAdjustRecord" header-align="center" align="center" label="库存调整记录" :key="Math.random()">
				<template slot-scope="scope" v-if="scope.row.stockAdjustRecord">
					<el-tooltip placement="top">
						<div slot="content">
							<p>时间+初始+修改+最终</p>
							<p v-for="item in scope.row.stockAdjustRecord">{{item}}</p>
						</div>
						<p>{{scope.row.stockAdjustRecord[1]}}</p>
					</el-tooltip>
				</template>
			</el-table-column>
			<el-table-column prop="stockEnableStatus" v-if="showlist.stockEnableStatus" header-align="center" align="center" label="库存启用状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.stockEnableStatus == 0">启用</span>
					<span v-else-if="scope.row.stockEnableStatus == 1">不启用</span>
				</template>
			</el-table-column>
			<el-table-column prop="stockDays" v-if="showlist.stockDays" header-align="center" align="center" label="库存天数">
			</el-table-column>
			<el-table-column prop="occupyStockType" v-if="showlist.occupyStockType" header-align="center" align="center" label="库存扣除类型">
			</el-table-column>
			<el-table-column prop="deleteStatus" v-if="showlist.deleteStatus" header-align="center" align="center" label="删除状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.deleteStatus == 0">正常</span>
					<span v-else-if="scope.row.deleteStatus == 1">删除</span>
				</template>
			</el-table-column>
			<el-table-column prop="status" v-if="showlist.status" header-align="center" align="center" label="状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>
			<el-table-column prop="createUserType" v-if="showlist.createUserType" header-align="center" align="center" label="创建人类型" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.createUserType == 0">平台</span>
					<span v-else-if="scope.row.createUserType == 1">商家</span>
					<span v-else-if="scope.row.createUserType == 1">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="createUser" v-if="showlist.createUser" header-align="center" align="center" label="上传人">
			</el-table-column>
			<el-table-column prop="createTime" v-if="showlist.createTime" header-align="center" align="center" label="上传时间">
			</el-table-column>
			<el-table-column prop="updateUserType" v-if="showlist.updateUserType" header-align="center" align="center" label="更新人类型" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.updateUserType == 0">平台</span>
					<span v-else-if="scope.row.updateUserType == 1">商家</span>
					<span v-else-if="scope.row.updateUserType == 1">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateUser" v-if="showlist.updateUser" header-align="center" align="center" label="更新人">
			</el-table-column>
			<el-table-column prop="updateTime" v-if="showlist.updateTime" header-align="center" align="center" label="更新时间" :key="Math.random()">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="acceptStatus" v-if="showlist.acceptStatus" header-align="center" align="center" label="是否自动接单">
			</el-table-column>
			<el-table-column prop="acceptDelay" v-if="showlist.acceptDelay" header-align="center" align="center" label="自动接单延迟时间">
			</el-table-column>
			<el-table-column prop="returnStockStatus" v-if="showlist.returnStockStatus" header-align="center" align="center" label="超时未支付是否取消预留">
			</el-table-column>
			<el-table-column prop="overtimeLength" v-if="showlist.overtimeLength" header-align="center" align="center" label="超时设置">
			</el-table-column>
			<el-table-column prop="listStatus" v-if="showlist.listStatus" header-align="center" align="center" label="上架状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.listStatus == 0">已上架</span>
					<span v-else-if="scope.row.listStatus == 1">未上架</span>
				</template>
			</el-table-column>
			<el-table-column prop="goodsEvaluate" v-if="showlist.goodsEvaluate" header-align="center" align="center" label="商品评价">
			</el-table-column>
			<el-table-column prop="note" v-if="showlist.note" header-align="center" align="center" label="备注">
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="numberHandle">库存调整</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>



		<el-dialog title="列选择" :visible.sync="linVisible" :append-to-body="false">
			<el-checkbox :indeterminate="isIndeterminate" v-model="checkAll" @change="handleCheckAllChange" style="margin-left: 30px;">全选</el-checkbox>
			<el-checkbox-group v-model="checkList" class="checkgroup">
				<el-checkbox v-for="(item,index) in options" :key="index" :label="item.name" style="margin-left: 30px;">{{item.value}}</el-checkbox>
			</el-checkbox-group>
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="linVisible = false">取消</el-button>
	        	<el-button type="primary" @click="linSubmit()">确定</el-button>
	      	</span>
		</el-dialog>

		<el-dialog title="库存调整" :visible.sync="stockVisible" :append-to-body="false">
			<el-input v-model="stockNumber" placeholder="调整数量"></el-input>
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="stockVisible = false">取消</el-button>
	        	<el-button type="primary" @click="stockSubmit()">确定</el-button>
	      	</span>
		</el-dialog>
	</div>
</template>

<script>
	import jsondata from '../../../../static/json/provinces.json'
	export default {
		data() {
			return {
				condition: {
					industryTypeCode: '',
					platformTypeCode: '',
					sellerCode: '',
					goodsTypeCode: '',
					listStatus: '',
					condition: ''
				},
				AddressList: [],
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				types: [],
				sellers: [],
				goodstypes: [],
				platforms: [],

				options: [{
						name: 'uuid',
						value: 'UUID'
					},
					{
						name: 'industryTypeCode',
						value: '行业类别编码'
					},
					{
						name: 'industryTypeName',
						value: '行业类别名称'
					},
					{
						name: 'platformTypeCode',
						value: '平台商品类别编码'
					},
					{
						name: 'platformTypeName',
						value: '平台商品类别名称'
					},
					{
						name: 'platformTypeDiscountScope',
						value: '平台商品类别折扣范围'
					},
					{
						name: 'sellerCode',
						value: '商家编码'
					},
					{
						name: 'sellerName',
						value: '商家名称'
					},
					{
						name: 'goodsTypeCode',
						value: '商品类别编码'
					},
					{
						name: 'goodsTypeName',
						value: '商品类别名称'
					},
					{
						name: 'goodsTypeDiscount',
						value: '商品类别折扣'
					},
					{
						name: 'goodsNo',
						value: '序号'
					},
					{
						name: 'goodsUnit',
						value: '商品单位'
					},
					{
						name: 'nonMemberPrice',
						value: '商品价格'
					},
					{
						name: 'memberPrice',
						value: '会员价'
					},
					{
						name: 'goodsLable',
						value: '商品标签'
					},
					{
						name: 'goodsUrl',
						value: '商品图标url'
					},

					{
						name: 'goodsFullName',
						value: '商品名称'
					},
					{
						name: 'goodsCode',
						value: '商品编码'
					},
					{
						name: 'goodsDescribe',
						value: '商品描述'
					},
					{
						name: 'goodsLinkUrl',
						value: '商品链接'
					},
					{
						name: 'linkStatus',
						value: '链接状态'
					},
					{
						name: 'manageType',
						value: '经营模式'
					},
					{
						name: 'serveTime',
						value: '营业时间'
					},
					{
						name: 'serveStatus',
						value: '营业状态'
					},
					{
						name: 'billStatus',
						value: '是否提供发票'
					},
					{
						name: 'supplyTime',
						value: '供应时间'
					},

					{
						name: 'validTime',
						value: '有效期'
					},
					{
						name: 'stockDate',
						value: '库存日期'
					},
					{
						name: 'stockCode',
						value: '库存编号'
					},
					{
						name: 'stockAdjust',
						value: '库存调整'
					},

					{
						name: 'stockAdjustRecord',
						value: '库存调整记录'
					},
					{
						name: 'stockVersion',
						value: '库存版本'
					},
					{
						name: 'stockRule',
						value: '库存规则'
					},
					{
						name: 'stockQuantity',
						value: '库存数量'
					},
					{
						name: 'nowStockQuantity',
						value: '实时库存'
					},
					{
						name: 'initialStockQuantity',
						value: '初始化库存数量'
					},
					{
						name: 'stockEnableStatus',
						value: '库存启用状态'
					},
					{
						name: 'stockDays',
						value: '库存天数'
					},
					{
						name: 'occupyStockType',
						value: '库存扣除类型'
					},
					{
						name: 'deleteStatus',
						value: '删除状态'
					},
					{
						name: 'status',
						value: '状态'
					},
					{
						name: 'createUserType',
						value: '创建人类型'
					},
					{
						name: 'createUser',
						value: '上传人'
					},
					{
						name: 'createTime',
						value: '上传时间'
					},
					{
						name: 'updateUserType',
						value: '更新人类型'
					},
					{
						name: 'updateUser',
						value: '更新人'
					},
					{
						name: 'updateTime',
						value: '更新时间'
					},
					{
						name: 'acceptStatus',
						value: '是否自动接单'
					},
					{
						name: 'acceptDelay',
						value: '自动接单延迟时间'
					},
					{
						name: 'returnStockStatus',
						value: '超时未支付是否取消预留'
					},
					{
						name: 'overtimeLength',
						value: '超时设置'
					},
					{
						name: 'listStatus',
						value: '上架状态'
					}, {
						name: 'goodsEvaluate',
						value: '商品评价'
					},
					{
						name: 'note',
						value: '备注'
					},

				],

				showlist: {
					uuid: false,
					industryTypeCode: false,
					industryTypeName: true,
					platformTypeCode: false,
					platformTypeName: true,
					platformTypeDiscountScope: true,
					sellerCode: false,
					sellerName: true,
					goodsTypeCode: false,
					goodsTypeName: true,
					goodsTypeDiscount: true,
					goodsNo: false,
					goodsUnit: true,
					nonMemberPrice: true,
					memberPrice: true,
					goodsLable: false,
					goodsUrl: false,
					goodsFullName: false,
					goodsCode: false,
					goodsDescribe: false,
					linkStatus: false,
					goodsLinkUrl: false,
					manageType: false,
					serveTime: false,
					serveStatus: false,
					billStatus: false,
					supplyTime: true,
					validTime: true,
					stockDate: true,
					stockCode: true,
					stockAdjust: true,
					stockAdjustRecord: true,
					stockVersion: false,
					stockRule: false,
					stockQuantity: false,
					nowStockQuantity: true,
					initialStockQuantity: true,
					stockEnableStatus: false,
					stockDays: false,
					occupyStockType: false,
					deleteStatus: false,
					status: false,
					createUserType: false,
					createUser: false,
					createTime: false,
					updateUserType: false,
					updateUser: false,
					updateTime: false,
					acceptStatus: false,
					acceptDelay: false,
					returnStockStatus: false,
					overtimeLength: false,
					listStatus: false,
					goodsEvaluate: false,
					note: false
				},
				linVisible: false,
				isIndeterminate: false,
				checkAll: false,
				checkList: ['industryTypeName', 'platformTypeName', 'platformTypeDiscountScope', 'sellerName', 'goodsTypeName', 'goodsTypeDiscount', 'goodsFullName', 'goodsUnit', 'nonMemberPrice', 'memberPrice', 'supplyTime', 'validTime', 'stockDate', 'stockCode', 'nowStockQuantity', 'initialStockQuantity', 'stockAdjust', 'stockAdjustRecord'],
				stockVisible: false,
				stockNumber: '',
				idlist: [],

			};
		},
		activated() {
			this.getDataList();
			this.getTypes()
			this.AddressList = jsondata
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition = {
					industryTypeCode: '',
					platformTypeCode: '',
					sellerCode: '',
					goodsTypeCode: '',
					listStatus: '',
					condition: ''
				}
			},
			// 列选择
			linHandle() {
				this.linVisible = true
			},
			handleCheckAllChange(val) {
				this.options.forEach(item => { //当全选被选中的时候，循环遍历源数据，把数据的每一项加入到默认选中的数组去
					this.checkList.push(item.name)
				})
				this.checkList = val ? this.checkList : []; //三元表达式，如果val的值为true，当前默认选中的值赋值给自身,如果为false，取消全选
				this.isIndeterminate = false;
			},
			// 列选择
			linSubmit() {
				this.$nextTick(() => {
					let that = this
					for(var key in that.showlist) {
						that.showlist[key] = false
					}
					that.checkList.forEach(function(e) {
						that.showlist[e] = true
					})
				})
				this.linVisible = false
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
				//获取平台类别
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: val
					})
				}).then(({
					data
				}) => {
					this.platforms = data.dataList
				});
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
			//获取商品类别
			sellerChange(val) {
				this.$http({
					url: this.$http.adornUrl("/sale/saleGoodsType/findBySellerCode"),
					method: "post",
					params: this.$http.adornParams({
						sellerCode: val
					})
				}).then(({
					data
				}) => {
					this.goodstypes = data.data
				});
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.$http({
					url: this.$http.adornUrl("/sale/saleStock/list"),
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

						this.dataList.forEach(item => { //当全选被选中的时候，循环遍历源数据，把数据的每一项加入到默认选中的数组去
							item.stockAdjustRecord = item.stockAdjustRecord.split(';')
						})
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
			//库存调整
			numberHandle() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				this.idlist = []
				this.stockVisible = true
				this.stockNumber = ''
				this.dataListSelections.forEach(item => { //当全选被选中的时候，循环遍历源数据，把数据的每一项加入到默认选中的数组去
					this.idlist.push(item.uuid)
				})
			},
			stockSubmit() {
				
				var formData = new FormData()
				formData.append('uuids',this.idlist)
				formData.append('stockQuantityOffset',this.stockNumber)
				this.$http({
					url: this.$http.adornUrl("/sale/saleStock/updateStockQuantity"),
					method: "post",
					data: formData
				}).then(({
					data
				}) => {
					this.$message({
						type: 'success',
						message: data.msg
					})
					this.stockVisible = false
					this.getDataList();
				});
			},
			// 删除
			deleteHandle(id) {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				this.$confirm(`确定对当前行进行操作?`, "提示", {
						confirmButtonText: "确定",
						cancelButtonText: "取消",
						type: "warning"
					})
					.then(() => {
						let ids = []
						this.dataListSelections.forEach(function(e) {
							ids.push(e.uuid)
						});
						var formData = new FormData();
						formData.append("ids", ids);
						this.$http.post(this.$http.adornUrl("/sale/saleGoods/delete"), formData)
							.then(({
								data
							}) => {
								if(data && data.code === 0) {
									this.$message({
										message: "操作成功",
										type: "success",
										duration: 1500,
										onClose: () => {
											this.getDataList();
										}
									});
								} else {
									this.$message.error(data.msg);
								}
							});
					})
					.catch(() => {});
			}
		}
	};
</script>

<style scoped>
	.mod-home {
		line-height: 1.5;
	}
</style>