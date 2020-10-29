<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>商品添加</h4>
		</div>
		<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="140px" class="w-100">
			<el-row>
				<el-col :span="12">
					<el-form-item label="商家名称" prop="sellerCode">
						<el-select v-model="dataForm.sellerCode" clearable placeholder="商家名称" clearable class="w-100" @change="allSellerChange">
							<el-option v-for="(item,index) in allsellers" :key="index" :label="item.name" :value="item.value"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="商品名称" prop="goodsFullName">
						<el-input v-model="dataForm.goodsFullName" placeholder="商品名称" :disable="true"></el-input>
					</el-form-item>
					<el-form-item label="商品类别折扣" prop="goodsTypeDiscount">
						<el-input v-model="dataForm.goodsTypeDiscount" placeholder="商品类别折扣" :disabled="true"></el-input>
					</el-form-item>
					<el-form-item label="商品价格" prop="nonMemberPrice">
						<el-input v-model="dataForm.nonMemberPrice" placeholder="商品价格" @input="changedatapri"></el-input>
					</el-form-item>
					<el-form-item label="库存规则" prop="stockRule">
						<el-select v-model="dataForm.stockRule" clearable placeholder="库存规则" class="w-100">
							<el-option label="无库存" value="0"></el-option>
							<el-option label="总库存" value="1"></el-option>
							<el-option label="固定库存" value="2"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="供应时间" prop="supplyTime">
						<el-input v-model="dataForm.supplyTime" size="mini" placeholder="供应时间"></el-input>
					</el-form-item>
					<el-form-item label="上架状态" prop="listStatus">
						<el-radio-group v-model="dataForm.listStatus">
							<el-radio :label="0">未上架</el-radio>
							<el-radio :label="1">已上架</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="商品标签" prop="goodsLable">
						<el-input v-model="dataForm.goodsLable" placeholder="商品标签"></el-input>
					</el-form-item>
					<el-form-item label="序号" prop="goodsNo">
						<el-input v-model="dataForm.goodsNo" placeholder="序号"></el-input>
					</el-form-item>
					<el-form-item label="商品图标url" prop="goodsUrl">
						<el-button @click="showUpload()" size="mini" type="primary">添加</el-button>
					</el-form-item>
					<el-form-item label="状态" prop="status">
						<el-radio-group v-model="dataForm.status">
							<el-radio :label="0">启用</el-radio>
							<el-radio :label="1">禁用</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="特殊规格与价格">
						<el-radio-group v-model="actived" @change="spchange()">
							<el-radio :label="1">启用</el-radio>
							<el-radio :label="0">禁用</el-radio>
						</el-radio-group>
					</el-form-item>
					<el-form-item label="" v-if="specshow" style="width: 1100px;">
						<el-button type="primary" @click="addlist()" size="mini" style="margin-bottom: 20px;">添加</el-button>
						<ul class="detailform">
							<div class="item">规格</div>
							<div class="item">商品价格</div>
							<div class="item">会员价</div>
							<div class="item">库存规则</div>
							<div class="item">库存数量</div>
							<div class="item">操作</div>
						</ul>
						<ul class="detailform">
							<li v-for="(item,index) in detailsList">
								<div class="item">
									<el-tag :key="tag" v-for="tag in item.specList" closable :disable-transitions="false" mini @close="handleClose(tag,index)">
										{{tag}}
									</el-tag>
									<el-input class="input-new-tag" v-model="item.inputValue" size="mini" @keyup.enter.native="handleInputConfirm(index)" @blur="handleInputConfirm(index)">
									</el-input>
								</div>
								<div class="item">
									<el-input v-model="item.nonMemberPrice" size="mini" placeholder="商品价格" @input="changepri(index)"></el-input>
								</div>
								<div class="item">
									<el-input v-model="item.memberPrice" size="mini" placeholder="会员价" :disabled="true"></el-input>
								</div>
								<div class="item">
									<el-select v-model="item.stockRule" clearable placeholder="库存规则" size="mini" class="w-100">
										<el-option label="无库存" value="0"></el-option>
										<el-option label="总库存" value="1"></el-option>
										<el-option label="固定库存" value="2"></el-option>
									</el-select>
								</div>
								<div class="item">
									<el-input v-model="item.stockQuantity" size="mini" placeholder="库存数量"></el-input>
								</div>
								<div class="item">
									<el-button size="mini" @click="delspec(index)" type="danger">删除</el-button>
									<el-button size="mini" @click="showpri(index)" type="primary" v-if="item.nonMemberPrice>0">特殊价格</el-button>
								</div>
							</li>
						</ul>
					</el-form-item>
				</el-col>
				<el-col :span="12">
					<el-form-item label="商品类别" prop="goodsTypeCode">
						<el-select v-model="dataForm.goodsTypeCode" clearable class="w-100" filterable placeholder="商品类别" value-key="id" @change="typehandel">
						 	<el-option v-for="item in allGoodstypes" :key="item.discount" :label="item.name" :value="item.value"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="商品单位" prop="goodsUnit">
						<el-select v-model="dataForm.goodsUnit" clearable placeholder="商品单位" clearable class="w-100">
							<el-option v-for="item in units" :key="item.value" :label="item.label" :value="item.label"></el-option>
						</el-select>
					</el-form-item>
					<el-form-item label="会员价" prop="memberPrice">
						<el-input v-model="dataForm.memberPrice" placeholder="会员价" :disabled="true"></el-input>
					</el-form-item>
					<el-form-item label="库存数量" prop="stockQuantity">
						<el-input v-model="dataForm.stockQuantity" placeholder="库存数量"></el-input>
					</el-form-item>
					
					<el-form-item label="必购状态" prop="mustStatus">
						<el-radio-group v-model="dataForm.mustStatus">
							<el-radio :label="0">非必购</el-radio>
							<el-radio :label="1">必购</el-radio>
						</el-radio-group>
					</el-form-item>
					
					<!--<el-form-item label="有效期" prop="validTime">
						<el-input v-model="dataForm.validTime" placeholder="有效期"></el-input>
					</el-form-item>-->
					<el-form-item label="商品评价" prop="goodsEvaluate">
						<el-input v-model="dataForm.goodsEvaluate" placeholder="商品评价"></el-input>
					</el-form-item>
					<el-form-item label="商品描述" prop="goodsDescribe">
						<el-input v-model="dataForm.goodsDescribe" placeholder="商品描述"></el-input>
					</el-form-item>
					<el-form-item label="商品链接" prop="goodsLinkUrl">
						<el-input v-model="dataForm.goodsLinkUrl" placeholder="商品链接"></el-input>
					</el-form-item>
					<el-form-item label="链接状态" prop="linkStatus">
						<el-radio-group v-model="dataForm.linkStatus">
							<el-radio :label="0">不跳转</el-radio>
							<el-radio :label="1">跳转</el-radio>
						</el-radio-group>
					</el-form-item>
					<!--<el-form-item label="自动接单延迟时间" prop="acceptDelay">
						<el-input v-model="dataForm.acceptDelay" placeholder="自动接单延迟时间"></el-input>
					</el-form-item>-->
					<el-form-item label="备注" prop="note">
						<el-input v-model="dataForm.note" placeholder="备注"></el-input>
					</el-form-item>

					<!--<el-form-item label="库存启用状态" prop="stockEnableStatus">
						<el-radio-group v-model="dataForm.stockEnableStatus">
							<el-radio :label="0">启用</el-radio>
							<el-radio :label="1">不启用</el-radio>
						</el-radio-group>
					</el-form-item>-->
					<!--<el-form-item label="库存扣除类型" prop="occupyStockType">
							<el-radio-group v-model="dataForm.occupyStockType">
								<el-radio :label="0">先扣库存后支付</el-radio>
								<el-radio :label="1">先支付后扣库存</el-radio>
							</el-radio-group>
						</el-form-item>-->

					<!--<el-form-item label="超时未支付是否取消预留" prop="returnStockStatus">
						<el-radio-group v-model="dataForm.returnStockStatus">
							<el-radio :label="0">否</el-radio>
							<el-radio :label="1">是</el-radio>
						</el-radio-group>
					</el-form-item>-->
					<!--<el-form-item label="超时设置" prop="overtimeLength">
						<el-input v-model="dataForm.overtimeLength" placeholder="超时设置"></el-input>
					</el-form-item>-->

				</el-col>
			</el-row>
		</el-form>
		<div slot="footer" class="formfooter">
			<el-button @click="emptyData()" size="mini">重置</el-button>
			<el-button type="primary" @click="dataFormSubmit()" size="mini">确定</el-button>
		</div>
		<el-dialog title="价格设置" :close-on-click-modal="false" :visible.sync="privisible" width="960px">
			<div class="week">
				<h4>设置周价</h4>
				<ul>
					<li>周一</li>
					<li>周二</li>
					<li>周三</li>
					<li>周四</li>
					<li>周五</li>
					<li>周六</li>
					<li>周日</li>
				</ul>
				<ul>
					<li>
						<el-input v-model="pricelist.mon"></el-input>元
					</li>
					<li>
						<el-input v-model="pricelist.tue"></el-input>元
					</li>
					<li>
						<el-input v-model="pricelist.wed"></el-input>元
					</li>
					<li>
						<el-input v-model="pricelist.thu"></el-input>元
					</li>
					<li>
						<el-input v-model="pricelist.fri"></el-input>元
					</li>
					<li>
						<el-input v-model="pricelist.sat"></el-input>元
					</li>
					<li>
						<el-input v-model="pricelist.sun"></el-input>元
					</li>
				</ul>
			</div>
			<div class="date">
				<h4>设置日期价格(<span>日期价格和周价重复，默认以显示日期价格为主</span>)</h4>
				<el-button type="primary" @click="addprilist()" size="mini" style="margin-bottom: 20px;">添加</el-button>
				<ul>
					<li v-for="(item,index) in datelist">
						<div class="item">
							<el-date-picker v-model="item.daterange" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期">
							</el-date-picker>
						</div>
						<div class="item">
							<el-input v-model="item.price" size="mini"></el-input>元
						</div>
						<div class="item">
							<el-button type="danger" @click="delpri(index)" size="mini">删除</el-button>
						</div>
					</li>
				</ul>
			</div>
			<span slot="footer" class="dialog-footer">
        		<el-button @click="privisible = false">取消</el-button>
        		<el-button type="primary" @click="priSubmit()">确定</el-button>
     		</span>
		</el-dialog>
		<el-dialog title="图片上传" :visible.sync="uploadVisible" :append-to-body="false" width="560px">
			<el-button type="primary" @click="addimg()" size="mini">添加</el-button>
			<ul class="filelist">
				<li v-for="(item,index) in uploadlist">
					<el-input v-model="item.attachmentName" placeholder="请输入标题" size="mini" class="title"></el-input>
					<div class="filebox">
						<input type="file" @change="handleFile" :id="index" accept="image/*" />
						<el-button type="primary" size="mini">上传</el-button>
					</div>
					<img :src="item.attachmentUrl" @click="showimg(item.attachmentUrl)" />
					<el-button type="danger" size="mini" @click="delitem(index)">删除</el-button>
				</li>
			</ul>
			<img />
			<span slot="footer" class="dialog-footer">
	        	<el-button @click="uploadVisible = false">取消</el-button>
	        	<el-button type="primary" @click="uploadSubmit()">确定</el-button>
	      	</span>
		</el-dialog>

		<el-dialog title="大图显示" :visible.sync="bigimgVisible" :append-to-body="false" width="560px">
			<img :src="tagUrl" width="100%" />
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				allsellers: [],
				allGoodstypes: [],
				units: [],
				dataForm: {
					uuid: 0,
					sellerCode: '',
					goodsTypeCode: '',
					goodsTypeDiscount: 100,
					goodsFullName: '',
					goodsUnit: '',
					supplyTime: '',
					validTime: '',
					listStatus: 0,
					goodsLable: '',
					goodsEvaluate: '',
					goodsNo: '',
					goodsUrl: '',
					goodsDescribe: '',
					linkStatus: 0,
					goodsLinkUrl: '',
					acceptStatus: 0,
					status: 0,
					note: '',
					acceptDelay: 5,
					goodsImageList: [],
					memberPrice: '',
					stockQuantity: '',
					mustStatus: 0
				},
				actived: 0,
				specshow: false,
				detailsList: [],
				uploadlist: [],
				inputVisible: false,
				privisible: false,
				uploadVisible: false,
				bigimgVisible: false,
				tagUrl: '',
				pricelist: {
					mon: 0,
					tue: 0,
					wed: 0,
					thu: 0,
					fri: 0,
					sat: 0,
					sun: 0
				},
				datelist: [],
				index: 0,
				dataRule: {
					sellerCode: [{
						required: true,
						message: '商家不能为空',
						trigger: 'change'
					}],
					goodsTypeCode: [{
						required: true,
						message: '商品类别不能为空',
						trigger: 'change'
					}],
					goodsFullName: [{
						required: true,
						message: '商品名称不能为空',
						trigger: 'blur'
					}],
					nonMemberPrice: [{
						required: true,
						message: '商品价格不能为空',
						trigger: 'blur'
					},{
						pattern: /^[1-9]\d*\,\d*|[0-9]\d*$/,
						message: '商品价格必须为数字值'
					}],
					goodsUnit: [{
						required: true,
						message: '商品单位不能为空',
						trigger: 'change'
					}],
					stockRule: [{
						required: true,
						message: '库存规则不能为空',
						trigger: 'change'
					}],
					stockQuantity: [{
						required: true,
						message: '库存数量不能为空',
						trigger: 'blur'
					}]
				}
			};
		},
		activated() {
			this.getUnits()
			this.showSeller()
			this.actived = 0
			this.specshow = false
			this.detailsList = []
			this.dataForm = {
				uuid: 0,
				sellerCode: '',
				goodsTypeCode: '',
				goodsTypeDiscount: 90,
				goodsFullName: '',
				goodsUnit: '',
				supplyTime: '',
				validTime: '',
				listStatus: 0,
				goodsLable: '',
				goodsEvaluate: '',
				goodsNo: '',
				goodsUrl: '',
				goodsDescribe: '',
				linkStatus: 0,
				goodsLinkUrl: '',
				acceptStatus: 0,
				status: 0,
				note: '',
				acceptDelay: 5,
				goodsImageList: [],
				mustStatus: 0
			}
		},
		methods: {
			//获取商品单位
			getUnits() {
				this.$http({
					url: this.$http.adornUrl("/sale/saleDict/findByParentCode"),
					method: "get",
					params: this.$http.adornParams({
						parentCode: 'unit'
					})
				}).then(({
					data
				}) => {
					this.units = data.dataList
					console.log(this.units)
				});
			},

			//表单内获取商品类别
			allSellerChange(val) {
				this.$http({
					url: this.$http.adornUrl("/sale/saleGoodsType/findBySellerCode"),
					method: "post",
					params: this.$http.adornParams({
						sellerCode: val
					})
				}).then(({
					data
				}) => {
					this.allGoodstypes = data.data
					console.log(this.allGoodstypes)
				});
			},
			//选择类别获取折扣
			typehandel(value) {
				let that = this
				this.allGoodstypes.forEach(function(e) {
					if(e.value == value) {
						that.dataForm.goodsTypeDiscount = e.discount
					}
				})
			},
			

			//显示所有商家
			showSeller() {
				this.$http({
					url: this.$http.adornUrl('/sale/saleSeller/querySaleSeller'),
					method: "post",
					params: this.$http.adornParams()
				}).then(({
					data
				}) => {
					this.allsellers = data.data
					console.log(this.allsellers)
				});
			},
			//显示隐藏商品规格编辑
			spchange() {
				if(this.actived == 1) {
					this.specshow = true
				} else {
					this.specshow = false
				}
			},
			//添加商品行
			addlist() {
				this.detailsList.push({
					spec: '',
					specList: [],
					stockRule: '0',
					specialPriceWeekArray: [], //周价格
					specialPriceDateArray: [], //日期价格
					stockQuantity: '',
					nonMemberPrice: 0,
					memberPrice: 0,
				})
			},
			//修改原价会员价自动变更
			changedatapri() {
				let num = Number(this.dataForm.nonMemberPrice) * Number(this.dataForm.goodsTypeDiscount) / 100
				this.dataForm.memberPrice = Math.ceil(num * 100) / 100
			},
			//特殊规格修改原价会员价自动变更
			changepri(index) {
				let num = Number(this.detailsList[index].nonMemberPrice) * Number(this.dataForm.goodsTypeDiscount) / 100
				this.detailsList[index].memberPrice = Math.ceil(num * 100) / 100
			},
			//商品规格操作
			handleClose(tag, index) {
				this.detailsList[index].specList.splice(this.detailsList[index].specList.indexOf(tag), 1);
			},

			handleInputConfirm(index) {
				let inputValue = this.detailsList[index].inputValue;
				if(inputValue) {
					this.detailsList[index].specList.push(inputValue);
				}
				this.inputVisible = false;
				this.detailsList[index].inputValue = '';
			},
			//删除规格
			delspec(index) {
				this.detailsList.splice(index, 1)
			},

			//打开价格设置
			showpri(index) {
				
				this.datelist = []
				this.privisible = true
				this.index = index

				let data = this.detailsList[index]
				if(data.specialPriceWeekArray[0]) {
					this.pricelist = {
						mon: data.specialPriceWeekArray[0],
						tue: data.specialPriceWeekArray[1],
						wed: data.specialPriceWeekArray[2],
						thu: data.specialPriceWeekArray[3],
						fri: data.specialPriceWeekArray[4],
						sat: data.specialPriceWeekArray[5],
						sun: data.specialPriceWeekArray[6]
					}
				} else {
					this.pricelist = {
						mon: 0,
						tue: 0,
						wed: 0,
						thu: 0,
						fri: 0,
						sat: 0,
						sun: 0
					}
				}

				let item = data.specialPriceDateArray
				if(item.length > 0) {
					for(var i = 0; i < item.length; i++) {
						var str = {
							daterange: [item[i].beginDate, item[i].endDate],
							beginDate: item[i].beginDate,
							endDate: item[i].endDate,
							price: item[i].price,
						}
						this.datelist.push(str)
					}
				} else {
					this.datelist = []
				}

			},
			//添加日期价格
			addprilist() {
				this.datelist.push({
					daterange: [],
					beginDate: '',
					endDate: '',
					price: 0,
				})
			},
			//删除日期价格
			delpri(index) {
				this.datelist.splice(index, 1)
			},
			//确定价格设置
			priSubmit() {
				this.detailsList[this.index].specialPriceWeekArray = [this.pricelist.mon, this.pricelist.tue, this.pricelist.wed, this.pricelist.thu, this.pricelist.fri, this.pricelist.sat, this.pricelist.sun]
				if(this.datelist.length > 0) {
					for(var i = 0; i < this.datelist.length; i++) {
						this.datelist[i].beginDate = this.datelist[i].daterange[0]
						this.datelist[i].endDate = this.datelist[i].daterange[1]
					}
					//深克隆
					this.detailsList[this.index].specialPriceDateArray = JSON.parse(JSON.stringify(this.datelist))
				} else {
					this.detailsList[this.index].specialPriceDateArray = []
				}
				this.privisible = false
			},
			//打开上传弹窗
			showUpload() {
				this.uploadVisible = true

			},
			addimg() {
				this.uploadlist.push({
					attachmentName: '',
					attachmentUrl: ''
				})
			},
			handleFile(e) {
				let index = e.target.id
				let $target = e.target || e.srcElement
				let file = $target.files[0]
				var formData = new FormData();
				formData.append("file", file);
				this.$http.post("http://192.168.1.121:13001/dwj-web-oss/oss/uploadFileToDir?dirName=goods", formData)
					.then(({
						data
					}) => {
						this.uploadlist[index].attachmentUrl = data.data
					});
			},
			//删除上传
			delitem(index) {
				this.uploadlist.splice(index, 1)
			},
			//上传图片提交
			uploadSubmit() {
				this.dataForm.goodsImageList = this.uploadlist
				this.uploadVisible = false
			},
			//图片放大
			showimg(url) {
				this.tagUrl = url
				this.bigimgVisible = true
			},
			emptyData() {
				this.dataForm = {
				uuid: 0,
				sellerCode: '',
				goodsTypeCode: '',
				goodsTypeDiscount: 90,
				goodsFullName: '',
				goodsUnit: '',
				supplyTime: '',
				validTime: '',
				listStatus: 0,
				goodsLable: '',
				goodsEvaluate: '',
				goodsNo: '',
				goodsUrl: '',
				goodsDescribe: '',
				linkStatus: 0,
				goodsLinkUrl: '',
				acceptStatus: 0,
				status: 0,
				note: '',
				acceptDelay: 5,
				goodsImageList: []
			}
			},
			//表单提交
			dataFormSubmit() {
				this.$refs["dataForm"].validate(valid => {
					if(valid) {
						if(this.actived == 0) {
							this.detailsList = []
						} else {
							for(var i = 0; i < this.detailsList.length; i++) {
								let dom = this.detailsList[i].specList
								var str = ''
								for(var n = 0; n < dom.length; n++) {
									str = str + dom[n] + ','
								}
								str = str.substr(0, str.length - 1)
								this.detailsList[i].spec = str
							}

							let flag = true
							for(var i = 0; i < this.detailsList.length; i++) {
								if(this.detailsList[i].spec == '' || this.detailsList[i].nonMemberPrice == '') {
									flag = false
								}
							}

							if(flag == false) {
								this.$message.error("规格或价格数据错误");
								return false;
							}
						}

						let obj = {
							saleGoods: this.dataForm,
							specList: this.detailsList
						}
						this.$http({
							url: this.$http.adornUrl('/sale/saleGoods/save'),
							method: "post",
							data: this.$http.adornData(obj)
						}).then(({
							data
						}) => {
							if(data && data.code === 0) {
								this.$message({
									message: "操作成功",
									type: "success",
									duration: 1500,
									onClose: () => {
										this.$router.push({
											path: 'sale-saleGoods'
										})
									}
								});

							} else {
								this.$message.error(data.msg);
							}
						});
					}
				});
			}
		}
	};
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
	.mod-home {
		line-height: 1.5;
	}
	
	.el-form-item {
		width: 400px;
	}
	
	.detailform {
		list-style: none;
		overflow: hidden;
		padding: 1px 10px;
		margin: 0;
		.item {
			float: left;
			width: 100px;
			height: 44px;
			box-shadow: 0 0 0 1px #f7f7f7;
			padding: 5px;
			margin: 0;
			text-align: center;
		}
		.item:nth-of-type(1) {
			width: 350px;
			.el-tag+.el-tag {
				margin-left: 10px;
			}
			.button-new-tag {
				margin-left: 10px;
				height: 28px;
				line-height: 23px;
				padding-top: 0;
				padding-bottom: 0;
			}
			.input-new-tag {
				width: 90px;
				margin-left: 10px;
				margin-top: 2px;
				vertical-align: bottom;
			}
		}
		.item:nth-last-of-type(1) {
			width: 180px;
		}
	}
	
	.filelist {
		list-style: none;
		li {
			height: 32px;
			margin-bottom: 10px;
			.title {
				width: 200px;
				float: left;
			}
			.filebox {
				float: left;
				position: relative;
				margin-left: 20px;
				margin-right: 20px;
				input {
					opacity: 0;
					position: absolute;
					left: 0;
					top: 0;
					width: 56px;
				}
			}
			img {
				margin-right: 20px;
				float: left;
				width: 28px;
				height: 28px;
				border-radius: 50%;
			}
		}
	}
	
	.week {
		ul {
			overflow: hidden;
			list-style: none;
			li {
				width: 110px;
				float: left;
				padding: 4px 5px;
				margin-right: 10px;
				border: 1px solid #ccc;
				.el-input {
					width: 80px;
					margin-right: 4px;
				}
			}
		}
		ul:nth-of-type(1) li {
			border: none;
		}
	}
	
	.date {
		h4 {
			span {
				color: red;
			}
		}
		li {
			overflow: hidden;
			margin-bottom: 20px;
			.item {
				float: left;
				.el-input {
					width: 80px;
					margin-left: 20px;
					margin-right: 4px;
				}
				.el-button {
					margin-left: 20px;
				}
			}
		}
	}
	
	.formfooter {
		float: right;
		padding-right: 20px;
		padding-bottom: 20px;
	}
</style>