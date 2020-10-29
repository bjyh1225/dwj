<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>会员列表</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="linHandle()">列选择</el-button>
			</div>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-select v-model="condition.userType" size="mini" clearable placeholder="角色">
						<el-option label="普通会员" value="0"></el-option>
						<el-option label="VIP会员" value="1"></el-option>
						<el-option label="商家" value="2"></el-option>
						<el-option label="市场专员" value="3"></el-option>
						<el-option label="市场经理" value="4"></el-option>
						<el-option label="平台" value="5"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.vipStatus" size="mini" clearable placeholder="是否会员">
						<el-option label="是" value="1"></el-option>
						<el-option label="否" value="0"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.userName" placeholder="昵称" clearable size="mini"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.userId" placeholder="会员编码" clearable size="mini"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.directCode" placeholder="推荐人编码" clearable size="mini"></el-input>
				</el-form-item>
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
			<el-table-column prop="userLogo" v-if="showlist.userLogo" header-align="center" align="center" width="80px" label="头像" :key="Math.random()">
				<template slot-scope="scope">
					<img :src="scope.row.userLogo" alt="" style="width: 60px;height: 60px;display: block;margin: 0 auto;border-radius: 50px;">
				</template>
			</el-table-column>
			<el-table-column prop="userName" v-if="showlist.userName" header-align="center" width="120px" align="center" label="昵称">
			</el-table-column>
			<el-table-column prop="userId" v-if="showlist.sellerNo" header-align="center" align="center" width="86px" label="用户编码">
			</el-table-column>
      <el-table-column prop="wxNick" v-if="showlist.wxNick" header-align="center" width="120px" align="left" label="微信信息">
              <template slot-scope="scope">
      					<span>昵称：{{scope.row.wxNick}}</span><br>
      					<span>省份：{{scope.row.province}}</span><br>
      					<span>城市：{{scope.row.city}}</span>
      				</template>
      </el-table-column>
      <el-table-column prop="userType" v-if="showlist.userType" header-align="center" align="center" width="80px" label="角色" :key="Math.random()">
        <template slot-scope="scope">
          <span v-if="scope.row.userType == 0">普通会员</span>
          <span v-else-if="scope.row.userType == 1">VIP会员</span>
          <span v-else-if="scope.row.userType == 2">商家</span>
          <span v-else-if="scope.row.userType == 3">市场专员</span>
          <span v-else-if="scope.row.userType == 4">市场经理</span>
          <span v-else-if="scope.row.userType == 5">平台</span>
        </template>
      </el-table-column>
      <el-table-column prop="vipStatus" v-if="showlist.vipStatus" header-align="center" align="center" width="55px" label="会员" :key="Math.random()">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.vipStatus === 0" type="danger" size="small">否</el-tag>
          <el-tag v-else size="small">是</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="cashAccount" v-if="showlist.cashAccount" header-align="center" width="80px" align="center" label="余额">
      </el-table-column>
			<el-table-column prop="directCode" v-if="showlist.directCode" header-align="center" width="86px" align="center" label="直推编码">
			</el-table-column>
      <el-table-column prop="indirectCode" v-if="showlist.indirectCode" header-align="center" width="86px" align="center" label="间推编码">
      </el-table-column>
			<el-table-column prop="directMemberNum" v-if="showlist.directMemberNum" header-align="center" width="138px" align="left" label="直推人数">
				<template slot-scope="scope">
					<span>普通会员：{{scope.row.directMemberNum}}人</span><br>
					<span>VIP会员：{{scope.row.directVipMemberNum}}人</span><br>
					<span>未签约商家：{{scope.row.directSellerNum}}人</span><br>
					<span>签约商家：{{scope.row.directSignSellerNum}}人</span><br>
				</template>
			</el-table-column>
			<el-table-column prop="completeOrderNum" v-if="showlist.completeOrderNum" header-align="center" width="68px" align="center" label="订单数">
			</el-table-column>
      <el-table-column prop="contactTelephone" v-if="showlist.contactTelephone" header-align="center" width="110px" align="center" label="手机号码">
      </el-table-column>
      <el-table-column prop="createTime" v-if="showlist.createTime" header-align="center" align="center" width="125px" label="注册时间" :key="Math.random()">
        <template slot-scope="scope">
          <span v-if="scope.row.createTime == 0"></span>
          <span v-else>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(scope.row.createTime).toTimeString().substr(0, 5)}}</span>
        </template>
      </el-table-column>
			<el-table-column prop="vipStartTime" v-if="showlist.vipStartTime" header-align="center" width="90px" align="center" label="会员日期" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.vipStartTime == 0"></span>
					<span v-else>{{new Date(scope.row.vipStartTime).toLocaleDateString().replace(/\//g, "-")}}<br>
					至<br>
					{{new Date(scope.row.vipEndTime).toLocaleDateString().replace(/\//g, "-")}}</span>
				</template>
			</el-table-column>
			<!--
			<el-table-column prop="vipEndTime" v-if="showlist.vipEndTime" header-align="center"  width="100px"  align="center" label="会员结束时间" :key="Math.random()" >
				<template slot-scope="scope">
					<span v-if="scope.row.vipEndTime == 0"></span>
					<span v-else>{{new Date(scope.row.vipEndTime).toLocaleDateString().replace(/\//g, "-")}}</span>
				</template>
			</el-table-column>
			-->
			<el-table-column prop="lastPayTime" v-if="showlist.lastPayTime" header-align="center" align="center" label="缴费时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.lastPayTime == 0"></span>
					<span v-else>{{new Date(scope.row.lastPayTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(scope.row.lastPayTime).toTimeString().substr(0, 8)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="lastPayAmount" v-if="showlist.lastPayAmount" header-align="center" align="center" label="缴费金额">
			</el-table-column>
			<!--<el-table-column prop="" v-if="showlist." header-align="center" align="center" label="缴费记录">
			</el-table-column>-->
			<el-table-column prop="loginName" v-if="showlist.loginName" header-align="center" align="center" label="登陆账号">
			</el-table-column>
			<el-table-column prop="loginPassword" v-if="showlist.loginPassword" header-align="center" align="center" label="登陆密码">
			</el-table-column>
			<el-table-column prop="email" v-if="showlist.email" header-align="center" align="center" label="联系邮箱">
			</el-table-column>
			<el-table-column prop="qq" v-if="showlist.qq" header-align="center" align="center" label="QQ">
			</el-table-column>
			<el-table-column prop="sellerStar" v-if="showlist.sellerStar" header-align="center" align="center" label="微信">
			</el-table-column>
			<el-table-column prop="address" v-if="showlist.address" header-align="center" align="center" label="地址">
			</el-table-column>
			<el-table-column prop="province" v-if="showlist.province" header-align="center" align="center" label="省">
			</el-table-column>
			<el-table-column prop="city" v-if="showlist.city" header-align="center" align="center" label="市">
			</el-table-column>
			<el-table-column prop="area" v-if="showlist.area" header-align="center" align="center" label="区">
			</el-table-column>
			<el-table-column prop="openId" v-if="showlist.openId" header-align="center" width="150px" align="left" label="微信openid">
			</el-table-column>
			<el-table-column prop="" header-align="center" align="center" label="操作" :key="Math.random()">
				<template slot-scope="scope">
					<el-button type="primary" size="mini" @click="changeHandle(scope.row.userId)">修改直推人</el-button>
				</template>
			</el-table-column>
		</el-table>
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
		<el-dialog title="直推人更改" :visible.sync="changeVisible" :append-to-body="false" header-align="center" align="center"  width="300px">
			<el-input v-model="directCode" placeholder="请填写直推人编码"></el-input>
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="changeVisible = false">取消</el-button>
	        	<el-button type="primary" @click="changeSubmit()">确定</el-button>
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
					address: [],
					lastPayTime: '',
					vipStartTime: '',
					vipStatus: '',
					userId: '',
					userName: '',
					userAccount: '',
					directUserName: '',
					legalPersonName: '',
					page: 1,
					size: 10
				},
				userid: '',
				options: [{
						name: 'userName',
						value: '姓名'
					},
					{
						name: 'sellerNo',
						value: '用户编码'
					},
					{
						name: 'userType',
						value: '角色'
					},
					{
						name: 'vipStatus',
						value: '是否会员'
					},
					{
						name: 'vipStartTime',
						value: '会员开始时间'
					},
					{
						name: 'vipEndTime',
						value: '会员结束时间'
					},
					{
						name: 'lastPayTime',
						value: '缴费时间'
					},
					{
						name: 'lastPayAmount',
						value: '缴费金额'
					},
					{
						name: 'createTime',
						value: '申请时间'
					},
					{
						name: 'contactTelephone',
						value: '联系电话'
					},
					{
						name: 'loginName',
						value: '登陆账号'
					},
					{
						name: 'loginPassword',
						value: '登陆密码'
					},
					{
						name: 'email',
						value: '联系邮箱'
					},
					{
						name: 'qq',
						value: 'QQ'
					},
					{
						name: 'sellerStar',
						value: '微信'
					},
					{
						name: 'address',
						value: '地址'
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
						name: 'directUserName',
						value: '推荐人ID'
					},
				],

				showlist: {
					userAccountType: true,
					userAccount: true,
					userName: true,
					userId: true,
					userType: true,
					vipStatus: true,
					sellerNo: true,
					vipStartTime: true,
					vipEndTime: true,
					lastPayTime: false,
					cashAccount: true,
					directCode: true, //推荐人编码
					directMemberNum: true, //直推会员人数
					directVipMemberNum: true, //直推VIP会员人数
					directSellerNum: true, //直推商家数
					userLogo: true, //头像
					completeOrderNum: true, //订单数量
					wxNick: true, //微信昵称
					openId: true, //openid
					lastPayAmount: false,
					createTime: true,
					contactTelephone: true,
					loginName: false,
					loginPassword: false,
					email: false,
					indirectCode:true,
					qq: false,
					sellerStar: false,
					address: false,
					province: false,
					city: false,
					area: false,
					directUserName: false
				},
				isIndeterminate: false,
				checkList: ['userName', 'vipStatus', 'sellerNo', 'vipStartTime', 'vipEndTime', 'lastPayTime', 'lastPayAmount', 'createTime', 'contactTelephone', 'loginName'],
				checkAll: false,
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				linVisible: false,
				changeVisible: false,
				directCode: ''
			};
		},
		activated() {
			this.getDataList();
			this.AddressList = jsondata
			this.getTypes()
		},
		methods: {
			searchHndel() {
				this.pageIndex = 1
				this.pageSize = 10
				this.getDataList()
			},
			searchReset() {
				this.condition = {
					address: [],
					lastPayTime: '',
					vipStartTime: '',
					vipStatus: '',
					userName: '',
					userAccount: '',
					directUserName: '',
					legalPersonName: '',
					userId: '',
					page: 1,
					size: 10
				}
			},
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

				this.$http({
					url: this.$http.adornUrl("/sale/saleBuyer/list"),
					method: "post",
					data: this.$http.adornData({
						page: this.pageIndex,
						size: this.pageSize,
						otherCondition: this.condition,
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
			changeHandle(id) {
				this.userid = id
				this.changeVisible = true
			},
			changeSubmit() {
				var formData = new FormData();
				formData.append("currentUser", this.userid);
				formData.append("parentUser", this.directCode);
				this.$http.post(this.$http.adornUrl("/sale/saleBuyer/unsafeUpdate"), formData)
					.then(({
						data
					}) => {
						if(data && data.code === 0) {
							this.$message({
								message: "操作成功",
								type: "success",
								duration: 1500,
								onClose: () => {
									this.directCode = ''
									this.changeVisible = false
									this.getDataList();
								}
							});
						} else {
							this.$message.error(data.msg);
						}
					});

			}
		}
	};
</script>

<style scoped>

</style>
