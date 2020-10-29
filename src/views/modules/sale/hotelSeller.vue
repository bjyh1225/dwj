<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>商家管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="linHandle()">列选择</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-input v-model="condition.shopName" placeholder="商家名称" clearable size="mini"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.legalPersonName" placeholder="法人代表姓名" clearable size="mini"></el-input>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.examineStatus" size="mini" clearable placeholder="审核状态">
						<el-option label="未提交" value="0"></el-option>
						<el-option label="提交审核" value="1"></el-option>
						<el-option label="审核通过" value="2"></el-option>
						<el-option label="审核驳回" value="3"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.listStatus" size="mini" clearable placeholder="上架状态">
						<el-option label="未上架" value="0"></el-option>
						<el-option label="已上架" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.recommendStatus" size="mini" clearable placeholder="推荐状态">
						<el-option label="未推荐" value="0"></el-option>
						<el-option label="已推荐" value="1"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.serveStatus" size="mini" clearable placeholder="营业状态">
						<el-option label="营业中" value="0"></el-option>
						<el-option label="未营业" value="1"></el-option>
					</el-select>
				</el-form-item>
					<!--
				<el-form-item>
					<el-date-picker v-model="condition.signTimes" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="至" start-placeholder="签约开始日期" end-placeholder="签约结束日期">
					</el-date-picker>
				</el-form-item>
				-->
				<el-form-item>
					<el-button @click="searchHndel()" size="mini" type="primary">查询</el-button>
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
      <el-table-column prop="logoUrl" v-if="showlist.logoUrl" header-align="center" align="center" width="90px" label="LOGO" :key="Math.random()">
        <template slot-scope="scope">
          <img :src="scope.row.logoUrl" alt="" style="width: 60px;height:60px;display: block;margin: 0 auto;">
        </template>
      </el-table-column>
			<el-table-column prop="sellerCode" v-if="showlist.sellerCode" header-align="center" align="center" label="商家编码" width="80px">
			</el-table-column>
			<el-table-column prop="fullName" v-if="showlist.fullName" header-align="center" align="center" width="110px" label="商家全称">
			</el-table-column>
			<el-table-column prop="displayLable" v-if="showlist.displayLable" header-align="center" width="90px" align="center" label="品类">
			</el-table-column>
      <el-table-column prop="address" v-if="showlist.address" width="150px" header-align="center" align="center" label="详细地址">
      </el-table-column>
			<el-table-column prop="circleName" v-if="showlist.circleName" header-align="center" width="90px" align="center" label="商圈">
			</el-table-column>
      <el-table-column prop="userId" v-if="showlist.userId" header-align="center" width="90px" align="center" label="会员编码">
      </el-table-column>
			<el-table-column prop="recommenderId" v-if="showlist.recommenderId" header-align="center" width="90px" align="center" label="推荐人">
			</el-table-column>
			<el-table-column prop="businessLicenseUrl" v-if="showlist.businessLicenseUrl" header-align="center" align="center" label="营业执照" :key="Math.random()">
				<template slot-scope="scope">
					<img :src="scope.row.businessLicenseUrl" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="healthLicenceUrl" v-if="showlist.healthLicenceUrl" header-align="center" align="center" label="卫生许可证" :key="Math.random()">
				<template slot-scope="scope">
					<img :src="scope.row.healthLicenceUrl" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="licenceUrl" v-if="showlist.licenceUrl" header-align="center" align="center" label="经营许可证" :key="Math.random()">
				<template slot-scope="scope">
					<img :src="scope.row.licenceUrl" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>

			<el-table-column prop="sellerStar" v-if="showlist.sellerStar" header-align="center" align="center" label="商家星级">
			</el-table-column>
			<el-table-column prop="sellerEvaluate" v-if="showlist.sellerEvaluate" header-align="center" align="center" label="商家评价">
			</el-table-column>
      <el-table-column prop="legalPersonName" v-if="showlist.legalPersonName" header-align="center" align="center" label="法人信息" width="110px">
         <template slot-scope="scope">
            <span>{{ scope.row.legalPersonName }}</span><br>
            <span>{{ scope.row.legalPersonPhone }}</span>
          </template>
      </el-table-column>
			<el-table-column prop="contactTelephone" v-if="showlist.contactTelephone" header-align="center" width="110px" align="center" label="联系电话">
			</el-table-column>
			<el-table-column prop="province" v-if="showlist.province" header-align="center" align="center" label="省">
			</el-table-column>
			<el-table-column prop="city" v-if="showlist.city" header-align="center" align="center" label="市">
			</el-table-column>
			<el-table-column prop="area" v-if="showlist.area" header-align="center" align="center" label="区">
			</el-table-column>
			<el-table-column prop="longitude" v-if="showlist.longitude" header-align="center" align="center" label="经度">
			</el-table-column>
			<el-table-column prop="latitude" v-if="showlist.latitude" header-align="center" align="center" label="纬度">
			</el-table-column>

			<el-table-column prop="lowestConsume" v-if="showlist.lowestConsume" header-align="center" align="center" label="人均消费">
			</el-table-column>
			<el-table-column prop="signStartTime" v-if="showlist.signStartTime" header-align="center"  width="140px" align="center" label="签约日期" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.signStartTime == 0">未签约</span>
					<span v-else>
					{{new Date(scope.row.signStartTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(scope.row.signStartTime).toTimeString().substr(0, 5)}}
					<br>↓<br>
					{{new Date(scope.row.signEndTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(scope.row.signEndTime).toTimeString().substr(0, 5)}}
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="stockDays" v-if="showlist.stockDays" header-align="center" align="center" label="库存天数">
			</el-table-column>
			<el-table-column prop="stockQuantity" v-if="showlist.stockQuantity" header-align="center" align="center" label="库存默认值">
			</el-table-column>
			<el-table-column prop="acceptDelay" v-if="showlist.acceptDelay" header-align="center" align="center" label="自动接单延迟时间">
			</el-table-column>
			<el-table-column prop="overtimeLength" v-if="showlist.overtimeLength" header-align="center" align="center" label="超时设置">
			</el-table-column>
			<el-table-column prop="applyTime" v-if="showlist.applyTime" header-align="center" align="center" label="申请时间" :key="Math.random()">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.applyTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.applyTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="examineStatus" v-if="showlist.examineStatus" header-align="center" align="center" width="80px" label="审核状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.examineStatus == 0">未提交</span>
					<span v-else-if="scope.row.examineStatus == 1">已提交</span>
					<span v-else-if="scope.row.examineStatus == 2">通过</span>
					<span v-else-if="scope.row.examineStatus == 3">驳回</span>
				</template>
			</el-table-column>
			<el-table-column prop="examineTime" v-if="showlist.examineTime" header-align="center" align="center" label="审核时间" :key="Math.random()">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.examineTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.examineTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="examineUser" v-if="showlist.examineUser" header-align="center" align="center" label="审核人">
			</el-table-column>
			<el-table-column prop="listStatus" v-if="showlist.listStatus" header-align="center" align="center" width="50px" label="上架" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.listStatus == 0">否</span>
					<span v-else-if="scope.row.listStatus == 1">是</span>
				</template>
			</el-table-column>
			<el-table-column prop="serveStatus" v-if="showlist.serveStatus" header-align="center" align="center" width="80px" label="营业状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.serveStatus == 0">营业中</span>
					<span v-else-if="scope.row.serveStatus == 1">未营业</span>
				</template>
			</el-table-column>
			<el-table-column prop="recommendStatus" v-if="showlist.recommendStatus" header-align="center" align="center" width="50px" label="推荐" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.recommendStatus == 0">否</span>
					<span v-else-if="scope.row.recommendStatus == 1">是</span>
				</template>
			</el-table-column>
			<el-table-column prop="serveTime" v-if="showlist.serveTime" header-align="center" width="90px" align="center" label="营业时间">
			</el-table-column>
			<el-table-column prop="acceptStatus" v-if="showlist.acceptStatus" width="80px" header-align="center" align="center" label="自动接单" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.acceptStatus == 0">否</span>
					<span v-else-if="scope.row.acceptStatus == 1">是</span>
				</template>
			</el-table-column>
			<el-table-column prop="settledType" v-if="showlist.settledType" header-align="center" align="center" label="入驻类型" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.settledType == 0">临时</span>
					<span v-else-if="scope.row.settledType == 1">永久</span>
				</template>
			</el-table-column>
			<el-table-column prop="bannerUrl" v-if="showlist.bannerUrl" header-align="center" align="center" label="横幅图片" :key="Math.random()">
				<template slot-scope="scope">
					<img :src="scope.row.bannerUrl" alt="" style="width: 100px;display: block;margin: 0 auto;">
				</template>
			</el-table-column>
			<el-table-column prop="introduction" v-if="showlist.introduction" header-align="center" align="center" label="店铺简介">
			</el-table-column>
			<el-table-column prop="deleteStatus" v-if="showlist.deleteStatus" header-align="center" align="center" label="删除状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.deleteStatus == 0">正常</span>
					<span v-else-if="scope.row.deleteStatus == 1">删除</span>
				</template>
			</el-table-column>
			<el-table-column prop="note" v-if="showlist.note" header-align="center" align="center" label="备注">
			</el-table-column>
			<el-table-column prop="status" v-if="showlist.status" header-align="center" align="center" label="状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.status == 0">启用</span>
					<span v-else-if="scope.row.status == 1">禁用</span>
				</template>
			</el-table-column>
			<el-table-column prop="sellerFraction" v-if="showlist.sellerFraction" header-align="center" width="55px" align="center" label="评分">
          <template slot-scope="scope">
            <span>{{ scope.row.sellerFraction }}分</span>
          </template>
			</el-table-column>
			<el-table-column prop="sellerHeat" v-if="showlist.sellerHeat" header-align="center" width="80px" align="center" label="热度">
			</el-table-column>
			<el-table-column prop="sellerTransactionIncomeAccount" v-if="showlist.sellerTransactionIncomeAccount" header-align="center" width="80px" align="center" label="累计收入">
			</el-table-column>
			<el-table-column prop="sellerTransactionAccount" v-if="showlist.sellerTransactionAccount" header-align="center" width="80px" align="center" label="账户余额">
			</el-table-column>
			<el-table-column prop="sellerTransactionTransferAccount" v-if="showlist.sellerTransactionTransferAccount" header-align="center" width="80px" align="center" label="累计提现">
			</el-table-column>
			<el-table-column prop="createUser" v-if="showlist.createUser" header-align="center" align="center" label="创建人">
			</el-table-column>
			<el-table-column prop="createTime" v-if="showlist.createTime" header-align="center" align="center" label="创建时间" :key="Math.random()">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="updateUser" v-if="showlist.updateUser" header-align="center" align="center" label="更新人">
			</el-table-column>
			<el-table-column prop="updateTime" v-if="showlist.updateTime" header-align="center" align="center" label="更新时间" :key="Math.random()">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.updateTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.updateTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="editHandle">修改</el-button>
			<el-button type="primary" size="mini" @click="passHandle">审核通过</el-button>
			<el-button type="danger" size="mini" @click="refuseHandle">审核驳回</el-button>
			<el-button type="primary" size="mini" @click="inHandel('1')">商家上架</el-button>
			<el-button type="danger" size="mini" @click="inHandel('0')">商家下架</el-button>
			<el-button type="danger" size="mini" @click="deleteHandle">商家删除</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>

		<el-dialog title="驳回备注" :close-on-click-modal="false" width="1000px" :visible.sync="noteshow" :append-to-body="false" class="info">
			<el-input v-model="examineNote" placeholder="驳回备注"></el-input>
			<span slot="footer" class="dialog-footer">
		        <el-button @click="noteshow = false">取消</el-button>
		        <el-button type="primary" @click="submit()">确定</el-button>
		    </span>
		</el-dialog>
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
	</div>
</template>

<script>
	import jsondata from '../../../../static/json/provinces.json'
	export default {
		data() {
			return {
				dataList: [],
				AddressList: [],

				condition: {
					name: '',
					address: [],
					shopName: '',
					legalPersonName: '',
					industryTypeCode: 'hotel',
					examineStatus: '',
					listStatus: '',
					recommendStatus: '',
					settledType: '',
					serveStatus: '',
					signTimes: [],
					page: 1,
					size: 10
				},
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				noteshow: false,
				examineNote: '',
				options: [{
						name: 'uuid',
						value: 'UUID'
					},
					{
						name: 'signStartTime',
						value: '签约日期'
					},
					{
						name: 'signEndTime',
						value: '签约结束日期'
					},
					{
						name: 'recommendStatus',
						value: '推荐状态'
					},
					{
						name: 'sellerNo',
						value: '商家序号'
					},
					{
						name: 'fullName',
						value: '商家全称'
					},
					{
						name: 'circleName',
						value: '商圈'
					},
					{
						name: 'sellerCode',
						value: '商家编码'
					},
					{
						name: 'displayLable',
						value: '商家标签'
					},
					{
						name: 'logoUrl',
						value: 'LOGO'
					},
					{
						name: 'businessLicenseUrl',
						value: '营业执照'
					},
					{
						name: 'healthLicenceUrl',
						value: '卫生许可证'
					},
					{
						name: 'licenceUrl',
						value: '经营许可证'
					},
					{
						name: 'legalPersonName',
						value: '法人姓名'
					},
					{
						name: 'legalPersonIdcard',
						value: '法人身份证号'
					},
					{
						name: 'sellerStar',
						value: '商家星级'
					},
					{
						name: 'sellerEvaluate',
						value: '商家评价'
					},
					{
						name: 'recommenderId',
						value: '推荐人'
					},
					{
						name: 'userId',
						value: '会员编码'
					},
					{
						name: 'contactTelephone',
						value: '联系电话'
					},
					{
						name: 'province',
						value: '省'
					},
					{
						name: 'city',
						value: '市'
					},
					{
						name: 'area',
						value: '区'
					},
					{
						name: 'address',
						value: '地址'
					},
					{
						name: 'longitude',
						value: '经度'
					},
					{
						name: 'latitude',
						value: '纬度'
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
						name: 'lowestConsume',
						value: '人均消费'
					},

					{
						name: 'stockDays',
						value: '库存天数'
					},

					{
						name: 'stockQuantity',
						value: '库存默认值'
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
						name: 'overtimeLength',
						value: '超时设置'
					},
					{
						name: 'loginName',
						value: '登陆账号'
					},
					{
						name: 'applyTime',
						value: '申请时间'
					},
					{
						name: 'examineStatus',
						value: '审核状态'
					},
					{
						name: 'examineTime',
						value: '审核时间'
					},
					{
						name: 'examineUser',
						value: '审核人'
					},
					{
						name: 'listStatus',
						value: '上架状态'
					},
					{
						name: 'settledType',
						value: '入驻类型'
					},
					{
						name: 'bannerUrl',
						value: '横幅图片'
					},
					{
						name: 'introduction',
						value: '店铺简介'
					},
					{
						name: 'deleteStatus',
						value: '删除状态'
					},

					{
						name: 'note',
						value: '备注'
					},
					{
						name: 'status',
						value: '状态'
					},
					{
						name: 'sellerFraction',
						value: '商家评分'
					},
					{
						name: 'sellerHeat',
						value: '商家热度'
					},
					{
						name: 'createUser',
						value: '创建人'
					},
					{
						name: 'createTime',
						value: '创建时间'
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
						name: 'sellerTransactionAccount',
						value: '账户余额'
					},
					{
						name: 'sellerTransactionIncomeAccount',
						value: '累计收入'
					},
					{
						name: 'sellerTransactionTransferAccount',
						value: '累计提现'
					},

				],
				showlist: {
					uuid: false,
					signTime: false,
					signStartTime: true,
					signEndTime: true,
					sellerTransactionAccount: false,
					sellerTransactionIncomeAccount: false,
					sellerTransactionTransferAccount: false,
					recommendStatus: true,
					sellerNo: true,
					fullName: true,
					circleName: true,
					sellerCode: true,
					displayLable: false,
					logoUrl: true,
					businessLicenseUrl: false,
					healthLicenceUrl: false,
					licenceUrl: false,
					legalPersonName: true,
					legalPersonIdcard: true,
					sellerStar: false,
					sellerEvaluate: false,
					recommenderId: true,
					userId: true,
					contactTelephone: true,
					province: false,
					city: false,
					area: false,
					address: true,
					longitude: false,
					latitude: false,
					serveTime: true,
					serveStatus: true,
					lowestConsume: false,
					stockDays: false,
					stockQuantity: false,
					acceptStatus: false,
					acceptDelay: false,
					overtimeLength: false,
					loginName: true,
					applyTime: false,
					examineStatus: true,
					examineTime: false,
					examineUser: false,
					listStatus: true,
					settledType: false,
					bannerUrl: false,
					introduction: false,
					deleteStatus: false,
					legalPersonPhone: true,
					note: false,
					status: false,
					sellerFraction: true,
					sellerHeat: true,
					createUser: false,
					createTime: false,
					updateUser: false,
					updateTime: false,
					displayLable: true,
				},
				linVisible: false,
				isIndeterminate: false,
				checkAll: false,
				checkList: ['logoUrl','examineStatus','circleName','displayLable','recommendStatus', 'signStartTime', 'sellerNo', 'fullName', 'sellerCode', 'legalPersonName', 'legalPersonIdcard', 'contactTelephone', 'serveStatus', 'loginName', 'listStatus', 'status', 'sellerHeat', 'sellerFraction'],
			};
		},
		activated() {
			this.getDataList();
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
					name: '',
					address: [],
					shopName: '',
					legalPersonName: '',
					legalPersonIdcard: '',
					industryTypeCode: 'carter',
					examineStatus: '',
					listStatus: '',
					recommendStatus: '',
					settledType: '',
					serveStatus: '',
					signTimes: [],
					page: 1,
					size: 10
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
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				this.condition.page = this.pageIndex
				this.condition.size = this.pageSize
				if(this.condition.signTimes) {
					this.condition.signTimes[0] = new Date(this.condition.signTimes[0]).getTime();
					this.condition.signTimes[1] = new Date(this.condition.signTimes[1]).getTime();
				}

				this.$http({
					url: this.$http.adornUrl("/sale/saleSeller/query"),
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

			//新增
			addHandle() {
				this.$router.push({
					path: 'sale-saleSellerAdd'
				})
			},

			passHandle() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}

				var sellerCodes = []
				for(var i = 0; i < this.dataListSelections.length; i++) {
					sellerCodes.push(this.dataListSelections[i].sellerCode)
				}

				this.$http({
					url: this.$http.adornUrl('/sale/saleSeller/examineSellerList'),
					method: "post",
					data: {
						'sellerCodes': sellerCodes
					}
				}).then(({
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

			},

			refuseHandle() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				if(this.dataListSelections.length > 1) {
					this.$message({
						type: 'error',
						message: '只能选择一条记录查看'
					})
					return false
				}

				if(this.dataListSelections[0].examineStatus == 2) {
					this.$message({
						type: 'error',
						message: '该记录已审核通过'
					})
					return false
				} else if(this.dataListSelections[0].examineStatus == 3) {
					this.$message({
						type: 'error',
						message: '该记录已被驳回'
					})
					return false
				} else if(this.dataListSelections[0].examineStatus == 0) {
					this.$message({
						type: 'error',
						message: '该记录还未通过第一次审核'
					})
					return false
				}

				this.noteshow = true
				this.examineNote = ''
			},

			submit() {
				this.$http({
					url: this.$http.adornUrl('/sale/saleSeller/refuseExamineSellerList'),
					method: "post",
					data: {
						'sellerCodes': [this.dataListSelections[0].sellerCode],
						'examineNote': this.examineNote
					}
				}).then(({
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
			},

			//修改
			editHandle() {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}
				if(this.dataListSelections.length > 1) {
					this.$message({
						type: 'error',
						message: '只能选择一条记录查看'
					})
					return false
				}
				sessionStorage.setItem('hotelSellerId', this.dataListSelections[0].uuid)
				this.$router.push({
					path: 'sale-hotelSellerEdit'
				})
			},
			deleteHandle() {
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
						this.$http.post(this.$http.adornUrl("/sale/saleSeller/delete"), formData)
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
			},

			//
			inHandel(type) {
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}

				var sellerCodes = []
				for(var i = 0; i < this.dataListSelections.length; i++) {
					sellerCodes.push(this.dataListSelections[i].sellerCode)
				}

				this.$http({
					url: this.$http.adornUrl('/sale/saleSeller/updateSellerListStatus'),
					method: "post",
					data: {
						'sellerCodes': sellerCodes,
						'listStatus': type
					}
				}).then(({
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
			},
		}
	};
</script>

<style scoped>

</style>
