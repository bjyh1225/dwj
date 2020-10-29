<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>商家修改</h4>
		</div>
		<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="140px" class="w-100 SellerForm">
			<el-row>
				<el-col :span="12">
					<el-form-item label="行业类别" prop="industryTypeCode">
						<el-select size="mini" v-model="dataForm.industryTypeCode" placeholder="行业类别" :disabled="true" clearable class="w-100">
							<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="商家全称" prop="fullName">
						<el-input v-model="dataForm.fullName" size="mini" placeholder="商家全称"></el-input>
					</el-form-item>
					<el-form-item label="联系电话" prop="contactTelephone">
						<el-input v-model="dataForm.contactTelephone" size="mini" placeholder="联系电话"></el-input>
					</el-form-item>
					<el-form-item label="坐标" prop="lating" style="width: 600px;">
						<el-input v-model="dataForm.lating" size="mini" placeholder="经纬度" :disabled="true" style="width: 234px;"></el-input>
						<el-button @click="editmapshow()" size="mini" type="primary">打开地图</el-button>
					</el-form-item>
					<el-form-item label="营业执照" prop="businessLicenseUrl" style="height: 36px;">
						<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="busImgs" :on-preview="busPreview" :on-success="handleAvatarSuccess" :before-upload="busUpload">
							<i class="el-icon-plus"></i>
						</el-upload>
					</el-form-item>
					<el-form-item label="卫生许可证" prop="healthLicenceUrl" style="height: 36px;">
						<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="heaImgs" :on-preview="heaPreview" :on-success="handleAvatarSuccess" :before-upload="heaUpload">
							<i class="el-icon-plus"></i>
						</el-upload>
					</el-form-item>
					<el-form-item label="法人姓名" prop="legalPersonName">
						<el-input v-model="dataForm.legalPersonName" size="mini" placeholder="法人姓名"></el-input>
					</el-form-item>
					<el-form-item label="法人手机号" prop="legalPersonPhone">
						<el-input v-model="dataForm.legalPersonPhone" size="mini" placeholder="法人手机号"></el-input>
					</el-form-item>
					<el-form-item label="法人身份证照片正面" prop="legalPersonIdcardUrl" style="height: 36px;">
						<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="legImgs" :on-preview="legPreview" :on-success="handleAvatarSuccess" :before-upload="legUpload">
							<i class="el-icon-plus"></i>
						</el-upload>
					</el-form-item>
					<el-form-item label="法人身份证照片反面" prop="legalPersonIdcardBackUrl" style="height: 36px;">
						<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="1" :file-list="legBackImgs" :on-preview="legBackPreview" :on-success="handleAvatarSuccess" :before-upload="legBackUpload">
							<i class="el-icon-plus"></i>
						</el-upload>
					</el-form-item>
					<el-form-item label="营业时间" prop="serveTime">
						<el-input v-model="dataForm.serveTime" size="mini" placeholder="营业时间"></el-input>
					</el-form-item>
					<el-form-item label="人均消费" prop="lowestConsume">
						<el-input v-model="dataForm.lowestConsume" size="mini" placeholder="人均消费"></el-input>
					</el-form-item>
					<el-form-item label="申请时间" prop="applyTime">
						<el-date-picker v-model="dataForm.applyTime" :disabled="true" type="datetime" placeholder="申请时间">
						</el-date-picker>
					</el-form-item>
					<el-form-item label="审核时间" prop="examineTime">
						<el-date-picker v-model="dataForm.examineTime" :disabled="true" type="datetime" placeholder="审核时间">
						</el-date-picker>
					</el-form-item>
					<el-form-item label="审核备注" prop="examineNote">
						<el-input v-model="dataForm.examineNote" :disabled="true" size="mini" placeholder="审核备注"></el-input>
					</el-form-item>
					<el-form-item label="上架状态" prop="listStatus">
						<el-select v-model="dataForm.listStatus" size="mini" clearable placeholder="请选择" class="w-100">
							<el-option label="未上架" value="0"></el-option>
							<el-option label="已上架" value="1"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="横幅图片" prop="bannerUrl">
						<el-upload :action="uploadUrl" list-type="picture-card" accept="image/*" :limit="5" :file-list="bannerImgs" :on-remove="handleRemove" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="bannerUpload">
							<i class="el-icon-plus"></i>
						</el-upload>
					</el-form-item>
					<el-form-item label="状态" prop="status">
						<el-radio-group v-model="dataForm.status">
							<el-radio :label="0">启用</el-radio>
							<el-radio :label="1">禁用</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="备注" prop="note">
						<el-input v-model="dataForm.note" size="mini" placeholder="备注"></el-input>
					</el-form-item>
					<el-form-item label="店铺标语" prop="ext1">
						<el-input v-model="dataForm.ext1" size="mini" placeholder="店铺标语"></el-input>
					</el-form-item>
					<el-form-item label="店铺简介" prop="introduction">
						<el-input type="textarea" :rows="4" v-model="dataForm.introduction" size="mini" placeholder="店铺简介"></el-input>
					</el-form-item>
					<el-form-item label="桌位费状态" prop="seatCostStatus">
						<el-radio-group v-model="dataForm.seatCostStatus">
							<el-radio :label="0">不收</el-radio>
							<el-radio :label="1">收取</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="桌位费类型" prop="seatCostType" v-if="dataForm.seatCostStatus == 1">
						<el-radio-group v-model="dataForm.seatCostType">
							<el-radio :label="0">按单收</el-radio>
							<el-radio :label="1">按人头</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="桌位费(元)" prop="seatCostAmount" v-if="dataForm.seatCostStatus == 1">
						<el-input v-model="dataForm.seatCostAmount" onkeyup="this.value= this.value.match(/\d+(\.\d{0,2})?/) ? this.value.match(/\d+(\.\d{0,2})?/)[0] : ''" size="mini" placeholder="桌位费"></el-input>
					</el-form-item>
					
				</el-col>
				<el-col :span="12">
					<el-form-item label="商家编码" prop="sellerCode">
						<el-input v-model="dataForm.sellerCode" size="mini" :disabled="true" placeholder="商家编码"></el-input>
					</el-form-item>
					<el-form-item label="推荐人ID" prop="recommenderId">
						<el-input v-model="dataForm.recommenderId" :disabled="true" size="mini" placeholder="推荐人"></el-input>
					</el-form-item>
					<el-form-item label="商圈" prop="circleCode">
						<el-select size="mini" v-model="dataForm.circleCode" placeholder="商圈" clearable class="w-100">
							<el-option v-for="item in allCircle" :key="item.circleCode" :label="item.circleName" :value="item.circleCode"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="详细地址" prop="address">
						<el-input v-model="dataForm.address" size="mini" placeholder="地址"></el-input>
					</el-form-item>
					
					<el-form-item label="商家logo" prop="logoUrl" style="height: 36px;">
						<el-upload :action="uploadUrl" list-type="picture-card" accept=".jpg,.jpeg,.png,.JPG,.JPEG," multiple :limit="1" :file-list="logoImgs" :on-preview="logoPreview" :on-success="handleAvatarSuccess" :before-upload="logoUpload">
							<i class="el-icon-plus"></i>
						</el-upload>
					</el-form-item>
					<el-form-item label="商家标签" prop="displayLable">
						<el-button type="primary" size="mini" @click="editLable()">编辑标签</el-button>
					</el-form-item>
					<el-form-item label="营业状态" prop="serveStatus">
						<el-select v-model="dataForm.serveStatus" size="mini" clearable placeholder="请选择" class="w-100">
							<el-option label="营业中" value="0"></el-option>
							<el-option label="未营业" value="1"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="审核状态" prop="examineStatus">
						<el-select v-model="dataForm.examineStatus" size="mini" :disabled="true" placeholder="请选择" class="w-100">
							<el-option label="申请入驻" value="0"></el-option>
							<el-option label="提交审核" value="1"></el-option>
							<el-option label="已审核" value="2"></el-option>
							<el-option label="已驳回" value="3"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="审核人" prop="examineUser">
						<el-input v-model="dataForm.examineUser" :disabled="true" size="mini" placeholder="审核人"></el-input>
					</el-form-item>
					<el-form-item label="签约开始时间" prop="signStartTime">
						<el-date-picker v-model="dataForm.signStartTime" :disabled="true" size="mini" type="datetime" placeholder="签约时间">
						</el-date-picker>
					</el-form-item>
					<el-form-item label="签约结束时间" prop="signEndTime">
						<el-date-picker v-model="dataForm.signEndTime" :disabled="true" size="mini" type="datetime" placeholder="签约时间">
						</el-date-picker>
					</el-form-item>
					<el-form-item label="下架备注" prop="offListNote">
						<el-input v-model="dataForm.offListNote" :disabled="true" size="mini" placeholder="下架备注"></el-input>
					</el-form-item>
					<el-form-item label="账户余额(元)" prop="sellerTransactionAccount">
						<el-input v-model="dataForm.sellerTransactionAccount" :disabled="true" size="mini" placeholder="账户余额"></el-input>
					</el-form-item>
					<el-form-item label="累计收入(元)" prop="sellerTransactionIncomeAccount">
						<el-input v-model="dataForm.sellerTransactionIncomeAccount" :disabled="true" size="mini" placeholder="收入"></el-input>
					</el-form-item>
					<el-form-item label="累计提现(元)" prop="sellerTransactionTransferAccount">
						<el-input v-model="dataForm.sellerTransactionTransferAccount" :disabled="true" size="mini" placeholder="提现"></el-input>
					</el-form-item>
					
					<el-form-item label="营业时间">
						<el-button type="primary" size="mini" @click="addtimes()">添加</el-button>
						<span v-for="(item,index) in serveTimeList" style="display: flex;">
							<el-time-picker style="margin-bottom: 10px;width: 260px;" size="mini" is-range v-model="item.time" range-separator="-" start-placeholder="开始时间" end-placeholder="结束时间" placeholder="选择时间范围">
							</el-time-picker>
							<i class="el-icon-error reduicon" @click="reduitem(index)"></i>
						</span>
					</el-form-item>
					<el-form-item label="是否自动接单" prop="acceptStatus">
						<el-radio-group v-model="dataForm.acceptStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="是否有订单打印机" prop="printOrderStatus">
						<el-radio-group v-model="dataForm.printOrderStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="是否有清单打印机" prop="printGoodsStatus">
						<el-radio-group v-model="dataForm.printGoodsStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="是否接单即打印订单" prop="acceptPrintOrderStatus">
						<el-radio-group v-model="dataForm.acceptPrintOrderStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="是否接单即打印清单" prop="acceptPrintGoodsStatus">
						<el-radio-group v-model="dataForm.acceptPrintGoodsStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>

					<el-form-item label="推荐状态" prop="recommendStatus">
						<el-radio-group v-model="dataForm.recommendStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>
					
				</el-col>
			</el-row>
		</el-form>
		<div slot="footer" class="formfooter">
			<el-button type="primary" @click="dataFormSubmit()" size="mini">确定</el-button>
		</div>
		<el-dialog :visible.sync="businessLicensible" :append-to-body="false">
			<img width="100%" :src="businessLicenseUrl" alt="">
		</el-dialog>
		<el-dialog :visible.sync="legalPersonIdcardble" :append-to-body="false">
			<img width="100%" :src="legalPersonIdcardUrl" alt="">
		</el-dialog>
		<el-dialog :visible.sync="legalPersonIdcardbackble" :append-to-body="false">
			<img width="100%" :src="dataForm.legalPersonIdcardBackUrl" alt="">
		</el-dialog>
		<el-dialog :visible.sync="healthLicenceble" :append-to-body="false">
			<img width="100%" :src="healthLicenceUrl" alt="">
		</el-dialog>
		<el-dialog :visible.sync="logoble" :append-to-body="false">
			<img width="100%" :src="logoUrl" alt="">
		</el-dialog>
		<el-dialog :visible.sync="bannerble" :append-to-body="false">
			<img width="100%" :src="bannerUrl" alt="">
		</el-dialog>
		<el-dialog title="地图选点" :close-on-click-modal="false" width="960px" :visible.sync="mapShow" :append-to-body="false">
			<div class="maptop">
				<input type="text" id="place" name="url" placeholder="请搜索门店地址">
				<el-input v-model="point.lat" size="mini" placeholder="经度" :disabled="true"></el-input>
				<el-input v-model="point.lng" size="mini" placeholder="纬度" :disabled="true"></el-input>
				<el-button type="primary" size="mini" @click="pointSubmit()">确定坐标</el-button>
			</div>
			<div id="container" style="width:920px;height:700px;position: relative;z-index: 1000;">

			</div>
		</el-dialog>

		<el-dialog title="标签编辑" :close-on-click-modal="false" width="960px" :visible.sync="lableShow" :append-to-body="false">
			<p>通用标签（单选）：</p>
			<ul class="labellist">
				<li v-for="(item,index) in labelList" :id='index' :class="index==sortcur?'active':''" @click="labelchange($event,item.label)">{{item.label}}</li>
			</ul>

			<p>商家专有设施标签：</p>
			<ul class="labellist">
				<li v-for="(item,index) in currList" :id='index' :class="sscur.indexOf(item.label) > -1 ?'active':''" @click="sslabelchange(item.label)">{{item.label}}</li>
			</ul>

			<p>行业通用设施标签：</p>
			<ul class="labellist">
				<li v-for="(item,index) in globalList" :id='index' :class="ttcur.indexOf(item.label) > -1 ?'active':''" @click="ttlabelchange(item.label)">{{item.label}}</li>
			</ul>
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="lableShow = false">取消</el-button>
	        	<el-button @click="lableShow = false" type="primary">确定</el-button>
	      	</span>
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				AddressList: [],
				city: '',
				mapShow: false,
				lableShow: false,
				labelcur: [],
				sscur: [],
				ttcur: [],
				point: {
					lat: 0,
					lng: 0
				},
				labelList: [],
				currList: [],
				globalList: [],

				types: [],
				allCircle: [],

				uploadUrl: 'http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller',

				businessLicenseUrl: '',
				businessLicensible: false,

				legalPersonIdcardUrl: '',
				legalPersonIdcardble: false,

				legalPersonIdcardBackUrl: '',
				legalPersonIdcardbackble: false,

				healthLicenceUrl: '',
				healthLicenceble: false,

				logoUrl: '',
				logoble: false,

				bannerUrl: '',
				bannerble: false,

				serveTimeList: [],
				dataForm: {
					industryTypeCode: '',
					industryTypeName: '',
					manageType: '0',
					fullName: '',
					sellerCode: '',
					displayLable: '',

					logoUrl: '',
					businessLicenseUrl: '',
					healthLicenceUrl: '',
					legalPersonName: '',
					legalPersonIdcard: '',
					legalPersonIdcardUrl: '',
					sellerEvaluate: '',
					recommenderId: '',
					contactTelephone: '',
					province: '',
					city: '',
					area: '',
					address: '',
					lating: '',
					longitude: '',
					latitude: '',
					serveTime: '',
					serveStatus: '',
					lowestConsume: '',
					validTime: '',
					returnStockStatus: 0,
					loginName: '',
					loginPassword: '',
					applyTime: '',
					examineStatus: '',
					examineTime: '',
					examineUser: '',
					listStatus: '',
					settledType: '',
					portalStatus: 0,
					bannerUrl: '',
					accountType: '',
					account: '',
					introduction: '',
					showGoods: '',
					note: '',
					status: 0,
					marketManager: '',
					examineNote: '',
					offListNote: '',
					shortSettledDate: '',
					longSettledDate: '',
					signStartTime: '',
					signEndTime: '',
					recommendStatus: 0,
					sellerTransactionAccount: '',
					sellerTransactionIncomeAccount: '',
					sellerTransactionTransferAccount: ''
				},
				emptyDate: {},
				dataRule: {
					industryTypeCode: [{
						required: true,
						message: '行业类别不能为空',
						trigger: 'change'
					}],
					fullName: [{
						required: true,
						message: '商家全称不能为空',
						trigger: 'blur'
					}],
					circleCode: [{
						required: true,
						message: '商圈不能为空',
						trigger: 'change'
					}],
					contactTelephone: [{
						required: true,
						message: '联系电话不能为空',
						trigger: 'blur'
					}],
					legalPersonIdcard: [{
						required: true,
						message: '法人身份证号不能为空',
						trigger: 'blur'
					}],
					loginName: [{
						required: true,
						message: '账号不能为空',
						trigger: 'blur'
					}],
					seatCostAmount: [{
						required: true,
						message: '桌位费不能为空',
						trigger: 'blur'
					}],

				},
				editId: '',
				busImgs: [],
				licImgs: [],
				legImgs: [],
				legBackImgs: [],
				heaImgs: [],
				logoImgs: [],
				bannerImgs: [],
				portalImgs: [],
				sortcur: -1
			};
		},
		activated() {
			this.editId = sessionStorage.getItem('hotelSellerId')
			this.getTypes()
			this.getFormData()
			this.emptyDate = this.dataForm
		},
		methods: {
			getCascaderObj(val, opt) {
				return val.map(function(value, index, array) {
					for(var itm of opt) {
						if(itm.value == value) {
							opt = itm.children;
							return itm;
						}
					}
					return null;
				});
			},
			getCircle(areaCode) {
				this.$http({
					url: this.$http.adornUrl("/sale/saleSeller/selectBusinessCircleListByArea"),
					method: "get",
					params: this.$http.adornParams({
						area: areaCode
					})
				}).then(({
					data
				}) => {
					this.allCircle = data.list
				});
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

			//表单上传点击查看图片
			busPreview(file) { //预览图片时调用
				this.businessLicenseUrl = file.url;
				this.businessLicensible = true;
			},

			legPreview(file) { //预览图片时调用
				this.legalPersonIdcardUrl = file.url;
				this.legalPersonIdcardble = true;
			},

			legBackPreview(file) { //预览图片时调用
				this.legalPersonIdcardBackUrl = file.url;
				this.legalPersonIdcardbackble = true;
			},

			heaPreview(file) { //预览图片时调用
				this.healthLicenceUrl = file.url;
				this.healthLicenceble = true;
			},
			logoPreview(file) { //预览图片时调用
				this.logoUrl = file.url;
				this.logoble = true;
			},
			bannerPreview(file) {
				this.bannerUrl = file.url;
				this.bannerble = true;
			},

			busUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.businessLicenseUrl = data.data
					});
			},

			legUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						console.log(data.data)
						this.dataForm.legalPersonIdcardUrl = data.data
					});
			},

			legBackUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						console.log(data.data)
						this.dataForm.legalPersonIdcardBackUrl = data.data
					});
			},

			heaUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.healthLicenceUrl = data.data
					});
			},
			logoUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						this.dataForm.logoUrl = data.data
					});
			},
			bannerUpload(file) {
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=seller", formData)
					.then(({
						data
					}) => {
						let bannerimg = {
							name: '横幅',
							url: data.data
						}
						this.bannerImgs.push(bannerimg)
					});
			},

			handleRemove(file) {
				for(var i = 0; i < this.bannerImgs.length; i++) {
					if(file.url == this.bannerImgs[i].url) {
						this.bannerImgs.splice(i, 1)
					}
				}
			},


			handleAvatarSuccess(file) { //图片上传成功

			},

			//切换行业清空标签
			industryChange() {
				//标签清空
				this.labelcur = []
				this.sscur = []
				this.ttcur = []
			},

			//标签修改
			editLable() {
				if(this.dataForm.industryTypeCode == '') {
					this.$message({
						type: 'error',
						message: '请先选择行业类别'
					})
					return false;
				}

				this.lableShow = true
				let Code = ''
				let serviceCode = ''
				if(this.dataForm.industryTypeCode == 'carter') {
					Code = 'carterLabel'
					serviceCode = 'carterServiceLabel'
				} else if(this.dataForm.industryTypeCode == 'hotel') {
					Code = 'hotelLabel'
					serviceCode = 'hotelServiceLabel'
				} else if(this.dataForm.industryTypeCode == 'ktv') {
					Code = 'happyLabel'
					serviceCode = 'happyServiceLabel'
				}
				//行业标签
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: Code
					})
				}).then(({
					data
				}) => {
					this.labelList = data.dataList
					for(var i = 0; i < this.labelList.length; i++) {
						if(this.labelList[i].label == this.dataForm.displayLable) {
							this.sortcur = i
							console.log(this.sortcur)
						}
					}
				});

				//行业服务设施标签
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: serviceCode
					})
				}).then(({
					data
				}) => {
					this.currList = data.dataList
				});

				//通用服务设施标签
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: "globalServiceLabel"
					})
				}).then(({
					data
				}) => {
					this.globalList = data.dataList
				});

				if(this.dataForm.displayLable) {
					this.labelcur = this.dataForm.displayLable.split(",")
				}

				if(this.dataForm.sellerLable) {
					this.sscur = this.dataForm.sellerLable.split(",")
				}

				if(this.dataForm.industryLable) {
					this.ttcur = this.dataForm.industryLable.split(",")
				}

			},

			labelchange(e,label) {
				this.sortcur = e.target.id
				this.dataForm.displayLable = label
			},

			sslabelchange(e,label) {
				if(this.sscur.indexOf(e) > -1) {
					let index = this.sscur.indexOf(e)
					this.sscur.splice(index, 1);
				} else {
					this.sscur.push(e)
				}
			},

			ttlabelchange(e,label) {
				if(this.ttcur.indexOf(e) > -1) {
					let index = this.ttcur.indexOf(e)
					this.ttcur.splice(index, 1);
				} else {
					this.ttcur.push(e)
				}
			},

			//获取表单信息
			getFormData() {
				this.$http({
					url: this.$http.adornUrl(`/sale/hotelSaleSeller/info/${this.editId}`),
					method: "get",
					params: this.$http.adornParams()
				}).then(({
					data
				}) => {
					this.getCircle(data.seller.area)
					this.dataForm = data.seller;

					this.dataForm.serveStatus = data.seller.serveStatus.toString()
					this.dataForm.examineStatus = data.seller.examineStatus.toString()
					this.dataForm.listStatus = data.seller.listStatus.toString()
					this.dataForm.settledType = data.seller.settledType.toString()
					this.dataForm.manageType = data.seller.manageType.toString()
					this.dataForm.accountType = data.seller.accountType.toString()

					if(this.dataForm.sellerLable) {
						this.sscur = this.dataForm.sellerLable.split(",")
					}

					if(this.dataForm.industryLable) {
						this.ttcur = this.dataForm.industryLable.split(",")
					}

					if(this.dataForm.latitude) {
						this.dataForm.lating = this.dataForm.latitude + ',' + this.dataForm.longitude
					} else {
						this.dataForm.lating = ''
					}

					if(data.seller.businessLicenseUrl) {
						let busimg = {
							name: '营业执照',
							url: data.seller.businessLicenseUrl
						}
						this.busImgs = []
						this.busImgs.push(busimg)
					}
					if(data.seller.legalPersonIdcardUrl) {
						let legimg = {
							name: '法人身份证正面',
							url: data.seller.legalPersonIdcardUrl
						}

						this.legImgs = []
						this.legImgs.push(legimg)
					}

					if(data.seller.legalPersonIdcardBackUrl) {
						let legimg = {
							name: '法人身份证反面面',
							url: data.seller.legalPersonIdcardBackUrl
						}

						this.legBackImgs = []
						this.legBackImgs.push(legimg)
					}

					if(data.seller.healthLicenceUrl) {
						let heaimg = {
							name: '卫生许可证',
							url: data.seller.healthLicenceUrl
						}

						this.heaImgs = []
						this.heaImgs.push(heaimg)
					}

					if(data.seller.logoUrl) {
						let logoimg = {
							name: '公司logo',
							url: data.seller.logoUrl
						}

						this.logoImgs = []
						this.logoImgs.push(logoimg)
					}

					if(data.seller.bannerUrl) {
						data.seller.bannerUrl = data.seller.bannerUrl.split(',')
						this.bannerImgs = []
						let that = this
						data.seller.bannerUrl.forEach(function(e) {
							let bannerimg = {
								name: '横幅',
								url: e
							}
							that.bannerImgs.push(bannerimg)
						})

					}
					
					
					//营业时间
					this.serveTimeList = []
					let serveTimeList = []
					if(data.seller.serveTimeList == '' || data.seller.serveTimeList == '[]') {
						serveTimeList = []
					} else {
						serveTimeList = JSON.parse(data.seller.serveTimeList)
						let len = serveTimeList.length
						for(var i = 0; i < len; i++) {
							serveTimeList[i] = serveTimeList[i].split(",")
							serveTimeList[i][0] = new Date(2020, 6, 12, Number(serveTimeList[i][0].split(":")[0]), Number(serveTimeList[i][0].split(":")[1]))
							serveTimeList[i][1] = new Date(2020, 6, 12, Number(serveTimeList[i][1].split(":")[0]), Number(serveTimeList[i][1].split(":")[1]))

							let item = {
								time: serveTimeList[i]
							}
							this.serveTimeList.push(item)
						}
					}


				});
			},
			
			addtimes() {
				if(this.serveTimeList.length == 3) {
					this.$message.error("最多只能添加3个时间段");
					return false;
				} else {
					let item = {
						time: [new Date(2020, 6, 12, 0, 0), new Date(2020, 6, 12, 23, 0)]
					}
					this.serveTimeList.push(item)
				}
			},
			reduitem(index) {
				this.serveTimeList.splice(index, 1)
			},
			
			//表单提交
			dataFormSubmit() {
				this.dataForm.serveTimeList = []
				if(this.serveTimeList.length == 0) {
					this.$message.error("营业时间不能为空");
					return false;
				} else {
					for(var i = 0; i < this.serveTimeList.length; i++) {
						let item = '"' + this.serveTimeList[i].time[0].toTimeString().substr(0, 5) + ',' + this.serveTimeList[i].time[1].toTimeString().substr(0, 5) + '"'
						this.dataForm.serveTimeList = this.dataForm.serveTimeList + item + ','
					}
					this.dataForm.serveTimeList = "[" + this.dataForm.serveTimeList.substring(0, this.dataForm.serveTimeList.length - 1) + "]"
				}
				
				this.dataForm.validTime = Number(this.dataForm.validTime)
				this.dataForm.applyTime = Number(this.dataForm.applyTime)
				this.dataForm.examineTime = Number(this.dataForm.examineTime)
				this.dataForm.longSettledDate = Number(this.dataForm.longSettledDate)
				this.dataForm.signTime = Number(this.dataForm.signTime)
				this.dataForm.shortSettledDate = Number(this.dataForm.shortSettledDate)

				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						
						//获取商圈名称
						this.allCircle.forEach(item => {
							if(item.circleCode == this.dataForm.circleCode) {
								this.dataForm.circleName = item.circleName
								this.dataForm.circleLatitude = item.circleLatitude
								this.dataForm.circleLongitude = item.circleLongitude
							}
						})
						
						//获取行业类别名字
						this.types.forEach(item => {
							if(item.value == this.dataForm.industryTypeCode) {
								this.dataForm.industryTypeName = item.label
							}
						})
						
						if(this.sscur.length == 0) {
							this.$message.error("商家专有设施标签不能为空");
							return false;
						}
						if(this.ttcur.length == 0) {
							this.$message.error("行业通用设施标签不能为空");
							return false;
						}

						//标签转字符串格式

						this.dataForm.sellerLable = this.sscur.toString()

						this.dataForm.industryLable = this.ttcur.toString()

						this.dataForm.bannerUrl = ''

						for(var i = 0; i < this.bannerImgs.length; i++) {
							this.dataForm.bannerUrl = this.bannerImgs[i].url + ',' + this.dataForm.bannerUrl
						}
						this.dataForm.bannerUrl = this.dataForm.bannerUrl.slice(0, this.dataForm.bannerUrl.length - 1)

						this.$http({
							url: this.$http.adornUrl('/sale/hotelSaleSeller/update'),
							method: "post",
							data: this.$http.adornData(this.dataForm)
						}).then(({
							data
						}) => {
							if(data && data.code === 0) {
								this.$message({
									message: "操作成功",
									type: "success",
									duration: 1500,
									onClose: () => {
										//标签清空
										this.labelcur = []
										this.sscur = []
										this.ttcur = []

										//上传图片清空

										this.busImgs = []
										this.licImgs = []
										this.legImgs = []
										this.legBackImgs = []
										this.heaImgs = []
										this.logoImgs = []
										this.bannerImgs = []
										this.portalImgs = []

										//表单坐标清空
										this.dataForm.latitude = ''
										this.dataForm.longitude = ''
										this.dataForm.lating = ''

										//地图弹窗清空坐标内容
										this.point.lat = ''
										this.point.lng = ''
										if(document.getElementById('place')) {
											document.getElementById('place').value = ''
										}

										this.$router.push({
											path: 'sale-hotelSeller'
										})
									}
								});
							} else {
								this.$message.error(data.msg);
							}
						});
					}
				});
			},

			init() {
				if(this.dataForm.address) {
					document.getElementById('place').value = this.dataForm.address
				}
				if(this.dataForm.latitude) {
					this.point.lat = this.dataForm.latitude
					this.point.lng = this.dataForm.longitude
					var map = new qq.maps.Map(document.getElementById("container"), {
						center: new qq.maps.LatLng(this.point.lat, this.point.lng),
						zoom: 18
					});
					var markersArray = []
					var that = this
					var anchor = new qq.maps.Point(6, 6),
						size = new qq.maps.Size(40, 40),
						origin = new qq.maps.Point(0, 0),
						icon = new qq.maps.MarkerImage('../../../static/img/point.gif', size, origin, anchor);

					var marker = new qq.maps.Marker({
						position: new qq.maps.LatLng(this.point.lat, this.point.lng),
						map: map,
						draggable: true,
						visible: true,
						icon: icon
					});

					qq.maps.event.addListener(marker, 'dragend', function() {
						var weizhi = marker.getPosition();
						map.setCenter(weizhi);
						that.point = weizhi
					});

					//实例化自动完成
					var ap = new qq.maps.place.Autocomplete(document.getElementById('place'), {
						offset: new qq.maps.Size(0, 5),
						location: this.city
					});

					var searchService = new qq.maps.SearchService({
						complete: function(results) {
							if(results.type === "CITY_LIST") {
								that.$message.error("当前检索结果分布较广，请指定城市进行检索")
								return;
							}
							var pois = results.detail.pois;
							that.point.lat = pois[0].latLng.lat
							that.point.lng = pois[0].latLng.lng
							var latlngBounds = new qq.maps.LatLngBounds();
							for(var i = 0, l = pois.length; i < l; i++) {
								var poi = pois[i];
								latlngBounds.extend(poi.latLng);
								var marker1 = new qq.maps.Marker({
									map: map,
									position: poi.latLng,
									visible: false
								});
								marker1.setTitle(poi.name);
							}
							map.fitBounds(latlngBounds);

							for(i in markersArray) {
								markersArray[i].setMap(null);
							}

							var marker2 = new qq.maps.Marker({
								position: new qq.maps.LatLng(pois[0].latLng.lat, pois[0].latLng.lng),
								map: map,
								icon: icon,
								draggable: true,
								visible: true,
							});
							markersArray.push(marker2)

							qq.maps.event.addListener(marker2, 'dragend', function() {
								var weizhi = marker2.getPosition();
								map.setCenter(weizhi);
								that.point = weizhi
							});
						}
					});
					//添加监听事件
					qq.maps.event.addListener(ap, "confirm", function(res) {
						searchService.search(res.value);
						marker.setMap(null)
					});

				} else {
					var map = new qq.maps.Map(document.getElementById("container"), {
						//以武汉为地图中心点
						center: new qq.maps.LatLng(30.5810841269, 114.316200103),
						zoom: 12
					});
					var markersArray = []
					var that = this
					var anchor = new qq.maps.Point(6, 6),
						size = new qq.maps.Size(40, 40),
						origin = new qq.maps.Point(0, 0),
						icon = new qq.maps.MarkerImage('../../../static/img/point.gif', size, origin, anchor);

					//实例化自动完成
					var ap = new qq.maps.place.Autocomplete(document.getElementById('place'), {
						offset: new qq.maps.Size(0, 5),
						location: this.city
					});

					var searchService = new qq.maps.SearchService({
						complete: function(results) {
							if(results.type === "CITY_LIST") {
								that.$message.error("当前检索结果分布较广，请指定城市进行检索")
								return;
							}
							var pois = results.detail.pois;
							that.point.lat = pois[0].latLng.lat
							that.point.lng = pois[0].latLng.lng
							var latlngBounds = new qq.maps.LatLngBounds();
							for(var i = 0, l = pois.length; i < l; i++) {
								var poi = pois[i];
								latlngBounds.extend(poi.latLng);
								var marker1 = new qq.maps.Marker({
									map: map,
									position: poi.latLng,
									visible: false
								});
								marker1.setTitle(poi.name);
							}
							map.fitBounds(latlngBounds);

							for(i in markersArray) {
								markersArray[i].setMap(null);
							}

							var marker2 = new qq.maps.Marker({
								position: new qq.maps.LatLng(pois[0].latLng.lat, pois[0].latLng.lng),
								map: map,
								icon: icon,
								draggable: true,
								visible: true,
							});
							markersArray.push(marker2)

							qq.maps.event.addListener(marker2, 'dragend', function() {
								var weizhi = marker2.getPosition();
								console.log(weizhi)
								map.setCenter(weizhi);
								that.point = weizhi
							});
						}
					});
					//添加监听事件
					qq.maps.event.addListener(ap, "confirm", function(res) {
						searchService.search(res.value);
					});
				}
			},
			//修改打开地图
			editmapshow() {
				this.mapShow = true
				this.$nextTick(function() {
					this.init()
				});
			},
			pointSubmit() {
				this.dataForm.latitude = this.point.lat
				this.dataForm.longitude = this.point.lng
				this.dataForm.lating = this.point.lat + ',' + this.point.lng
				if(this.dataForm.latitude) {
					this.mapShow = false
				} else {
					this.$message.error("请确定具体坐标");
				}
				let that = this
				$.ajax({
					url: "https://apis.map.qq.com/ws/geocoder/v1/",
					type: "get",
					dataType: 'jsonp',
					data: {
						location: this.point.lat + "," + this.point.lng,
						key: "FJDBZ-PXW64-USPUR-DQSWK-NJLGE-J2FBU",
						output: "jsonp"
					},
					success: function(res) {
						let addressinfo = res.result
						that.dataForm.address = addressinfo.address
						that.dataForm.province = addressinfo.ad_info.adcode.substring(0, 2)
						that.dataForm.provinceName = addressinfo.ad_info.province
						that.dataForm.city = addressinfo.ad_info.adcode.substring(0, 4)
						that.dataForm.cityName = addressinfo.ad_info.city
						that.dataForm.area = addressinfo.ad_info.adcode
						that.dataForm.areaName = addressinfo.ad_info.district
						console.log(that.dataForm)
						that.dataForm.circleCode = ''
						that.$http({
							url: that.$http.adornUrl("/sale/saleSeller/selectBusinessCircleListByArea"),
							method: "get",
							params: that.$http.adornParams({
								area: addressinfo.ad_info.adcode
							})
						}).then(({
							data
						}) => {
							that.allCircle = data.list
						});

					}
				});
			}
		}
	};
</script>

<style rel="stylesheet/scss" lang="scss">
	.SellerForm .el-form-item {
		width: 400px;
	}
	
	.mod-home {
		line-height: 1.5;
	}
	
	.el-upload-list__item {
		width: 100px !important;
		height: 36px !important;
	}
	.reduicon {
		color: red;
		font-size: 18px;
		margin-top: 3px;
		margin-left: 6px;
		cursor: pointer;
	}
	
	.maptop .el-input {
		display: inline-block;
		width: 120px;
		margin-left: 20px;
	}
	
	.maptop .el-button {
		float: right;
	}
	
	#place {
		background-color: #fff;
		background-image: none;
		border-radius: 4px;
		border: 1px solid #dcdfe6;
		box-sizing: border-box;
		color: #606266;
		display: inline-block;
		height: 28px;
		line-height: 28px;
		outline: 0;
		padding: 0 15px;
		width: 240px;
	}
	
	.el-upload--picture-card {
		border: 1px solid #c0ccda;
		width: 36px;
		height: 36px;
		line-height: 46px;
	}
	
	.labellist {
		overflow: hidden;
		display: flex;
		flex-wrap: wrap;
		justify-content: flex-start;
	}
	
	.labellist li {
		width: 100px;
		height: 34px;
		text-align: center;
		line-height: 30px;
		border: 2px solid #ece9e9;
		border-radius: 4px;
		margin: 6px;
		cursor: pointer;
	}
	
	.labellist li.active {
		border: 2px solid #19cc19;
	}
	
	.formfooter {
		float: right;
		padding-right: 20px;
		padding-bottom: 20px;
	}
	.mapwarp .el-dialog__body {
		padding-top: 0 !important;
	}
	.v-modal + div {
		z-index: 100000 !important;
		position: fixed !important;
	}
</style>