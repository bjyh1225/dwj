<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>申请入驻列表</h4>
		</div>
		<div class="tool-header">
			<el-form :inline="true" :model="condition" @keyup.enter.native="getDataList()">
				<el-form-item>
					<el-cascader size="mini" v-model="condition.addresslist" :options="AddressList" placeholder="区域" expand-trigger="hover" :clearable="true" :change-on-select="true"></el-cascader>
				</el-form-item>
				<el-form-item>
					<el-select size="mini" v-model="condition.industryTypeCode" placeholder="行业类别" clearable class="w-100">
						<el-option v-for="item in types" :key="item.value" :label="item.label" :value="item.value"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-select v-model="condition.examineStatus" size="mini" clearable placeholder="审核状态">
						<el-option label="申请入驻" value="0"></el-option>
						<el-option label="已审核" value="1"></el-option>
						<el-option label="已驳回" value="2"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.fullName" placeholder="商家名称" size="mini" class="w120"></el-input>
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
			<el-table-column prop="userId" header-align="center" align="center" width="86px" label="用户编号">
			</el-table-column>
			<el-table-column prop="recommenderId" header-align="center" align="center" width="86px" label="推荐人">
			</el-table-column>
			<el-table-column prop="industryTypeName" header-align="center" align="center" width="50px" label="行业">
			</el-table-column>
			<el-table-column prop="fullName" header-align="center" align="center" width="110px" label="商家全称">
			</el-table-column>
      <el-table-column prop="circleName" header-align="center" width="80px" align="center" label="商圈">
      </el-table-column>
			<el-table-column prop="address" header-align="center" align="center" label="详细地址" width="100px">
			</el-table-column>
      <el-table-column prop="sellerStar" header-align="center" width="90px" align="center" label="酒店星级">
      	<template slot-scope="scope">
      		<span v-if="scope.row.sellerStar == 1">经济/二星</span>
      		<span v-else-if="scope.row.sellerStar == 2">舒适/三星</span>
          <span v-else-if="scope.row.sellerStar == 3">高端/四星</span>
          <span v-else-if="scope.row.sellerStar == 4">豪华/五星</span>
          <span v-else >无</span>
      	</template>
      </el-table-column>
			<el-table-column prop="legalPersonName" header-align="center" align="center" width="108px" label="法人信息">
				<template slot-scope="scope">
					<span>{{scope.row.legalPersonName}}</span><br>
					<span>{{scope.row.legalPersonPhone}}</span><br>
				</template>
			</el-table-column>
			<el-table-column prop="contactTelephone" header-align="center" width="110px" align="center" label="联系人信息">
			</el-table-column>
			<el-table-column prop="businessLicenseUrl" header-align="center" align="center" width="100px" label="营业执照">
				<template slot-scope="scope">
					<a :href="scope.row.businessLicenseUrl" target=_blank><img :src="scope.row.businessLicenseUrl" alt="" style="width: 80px;display: block;margin: 0 auto;"></a>
				</template>
			</el-table-column>
			<el-table-column prop="healthLicenceUrl" header-align="center" align="center" width="100px" label="卫生许可证">
				<template slot-scope="scope">
          <span v-if="scope.row.healthLicenceUrl !== '' ">
            <a :href="scope.row.healthLicenceUrl" target=_blank><img :src="scope.row.healthLicenceUrl" alt="" style="width: 80px;display: block;margin: 0 auto;"></a>
          </span>
          <span v-else >无</span>
				</template>
			</el-table-column>
			<el-table-column prop="legalPersonIdcardUrl" header-align="center" align="center" width="100px" label="身份证正面">
				<template slot-scope="scope">
					<a :href="scope.row.legalPersonIdcardUrl" target=_blank><img :src="scope.row.legalPersonIdcardUrl" alt="" style="width: 80px;display: block;margin: 0 auto;"></a>
				</template>
			</el-table-column>
			<el-table-column prop="legalPersonIdcardBackUrl" header-align="center" align="center" width="100px" label="身份证反面">
				<template slot-scope="scope">
					<a :href="scope.row.legalPersonIdcardBackUrl" target=_blank><img :src="scope.row.legalPersonIdcardBackUrl" alt="" style="width: 80px;display: block;margin: 0 auto;"></a>
				</template>
			</el-table-column>
			<el-table-column prop="examineStatus" header-align="center" width="80px" align="center" label="审核状态">
				<template slot-scope="scope">
					<span v-if="scope.row.examineStatus == 0">申请入驻</span>
					<span v-else-if="scope.row.examineStatus == 1">已审核</span>
					<span v-else-if="scope.row.examineStatus == 2">已驳回</span>
				</template>
			</el-table-column>
			<el-table-column prop="applyTime" header-align="center" align="center" width="80px" label="申请时间">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.applyTime).toLocaleDateString().replace(/\//g, "-")}}<br>{{new Date(scope.row.applyTime).toTimeString().substr(0, 5)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="examineTime" header-align="center" align="center" width="80px" label="审核时间">
				<template slot-scope="scope">
					<span v-if="scope.row.examineTime == 0"></span>
					<span v-else>{{new Date(scope.row.examineTime).toLocaleDateString().replace(/\//g, "-")}}<br>{{new Date(scope.row.examineTime).toTimeString().substr(0, 5)}}</span>
				</template>
			</el-table-column>
			<!--<el-table-column prop="examineUser" header-align="center" align="center" label="审核人">
			</el-table-column>-->
			<el-table-column prop="examineNote" header-align="center" align="center" label="备注">
			</el-table-column>
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="infoHandle">详情</el-button>
			<el-button type="primary" size="mini" @click="acceptHandle">审核通过</el-button>
			<el-button type="danger" size="mini" @click="refuseHandle">审核驳回</el-button>
		</div>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
		<el-dialog title="详情" :close-on-click-modal="false" width="1000px" :visible.sync="infoshow" :append-to-body="false" class="info">
			<ul class="titlelist">
				<li>
					<span><span class="title">用户编码：</span>{{item.userId}}</span>
				</li>
				<li>
					<span><span class="title">商家全称：</span>{{item.fullName}}</span>
				</li>
				<li>
					<span><span class="title">省市区：</span>{{item.provinceName}}/{{item.cityName}}/{{item.areaName}}</span>
				</li>
				<li>
					<span><span class="title">地址：</span>{{item.address}}</span>
				</li>
				<li>
					<span><span class="title">行业类别编码：</span>{{item.industryTypeCode}}</span>
				</li>
				<li>
					<span><span class="title">行业类别名称：</span>{{item.industryTypeName}}</span>
				</li>
				<li>
					<span><span class="title">推荐人ID：</span>{{item.recommenderId}}</span>
				</li>
				<li>
					<span><span class="title">法人手机号：</span>{{item.legalPersonPhone}}</span>
				</li>
				<li>
					<span><span class="title">联系电话：</span>{{item.contactTelephone}}</span>
				</li>
				<li>
					<span><span class="title">申请时间：</span>{{new Date(item.applyTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(item.applyTime).toTimeString().substr(0, 8)}}</span>
				</li>
				<li>
					<span class="title">审核状态：</span>
					<span>
						<span v-if="item.examineStatus == 0">申请入驻</span>
					<span v-else-if="item.examineStatus == 1">已审核</span>
					<span v-else-if="item.examineStatus == 2">已驳回</span>
					</span>
				</li>
				<li>
					<span><span class="title1">营业执照：</span><img class="image" :src="item.businessLicenseUrl" @click="showimg(item.businessLicenseUrl)" /></span>
				</li>
				<li>
					<span><span class="title1">卫生许可证：</span><img class="image" :src="item.healthLicenceUrl" @click="showimg(item.healthLicenceUrl)" /></span>
				</li>
				<li>
					<span><span class="title1">身份证正面：</span><img class="image" :src="item.legalPersonIdcardUrl" @click="showimg(item.legalPersonIdcardUrl)" /></span>
				</li>
				<li>
					<span><span class="title1">身份证反面：</span><img class="image" :src="item.legalPersonIdcardBackUrl" @click="showimg(item.legalPersonIdcardBackUrl)" /></span>
				</li>
			</ul>
			<span slot="footer" class="dialog-footer" v-if="item.examineStatus == 0">
		        <el-button type="danger" @click="refuseHandle()">审核驳回</el-button>
		        <el-button type="primary" @click="acceptHandle()">审核通过</el-button>
		    </span>
		</el-dialog>
		<el-dialog title="驳回备注" :close-on-click-modal="false" width="1000px" :visible.sync="noteshow" :append-to-body="false" class="info">
			<el-input v-model="examineNote" placeholder="驳回备注"></el-input>
			<span slot="footer" class="dialog-footer">
		        <el-button @click="noteshow = false">取消</el-button>
		        <el-button type="primary" @click="submit()">确定</el-button>
		    </span>
		</el-dialog>
		<el-dialog title="图片查看" :close-on-click-modal="false" width="600px" :visible.sync="imgshow" :append-to-body="false">
			<div class="imgwarp">
				<img class="image" :src="curimg"/>
			</div>
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
				types: [],
				condition: {
					addresslist: [],
					industryTypeCode: '',
					examineStatus: '',
          sellerStar: '',
					fullName: ''
				},
				item: {},
				dataListSelections: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				infoshow: false,
				flag: false,
				examineNote: '',
				noteshow: false,
				imgshow: false,
				curimg: ''
			};
		},
		activated() {
			this.AddressList = jsondata
			this.getDataList()
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
					addresslist: [],
					industryTypeCode: '',
					examineStatus: '',
					province: '',
					city: '',
					area: '',
					fullName: ''
				}
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
			// 获取数据列表
			getDataList() {
				this.condition.province = ''
				this.condition.city = ''
				this.condition.area = ''

				if(this.condition.addresslist.length == 1) {
					this.condition.province = this.condition.addresslist[0]
				} else if(this.condition.addresslist.length == 2) {
					this.condition.city = this.condition.addresslist[1]
				} else if(this.condition.addresslist.length == 3) {
					this.condition.area = this.condition.addresslist[2]
				}
				this.dataListLoading = true;
				this.$http({
					url: this.$http.adornUrl("/sale/saleApplyInto/list"),
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
			infoHandle() {
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
				this.item = this.dataListSelections[0]
				this.infoshow = true
				console.log(this.item)
			},
			showimg(url){
				this.curimg = url
				this.imgshow = true
			},
			acceptHandle() {
				console.log(this.dataListSelections)
				if(this.dataListSelections.length === 0) {
					this.$message({
						type: 'error',
						message: '请至少选择一条记录查看'
					})
					return false
				}

				this.flag = false
				for(var i = 0; i < this.dataListSelections.length; i++) {
					if(this.dataListSelections[i].examineStatus != 0) {
						this.flag = true
					}
				}
				if(this.flag == true) {
					this.$message({
						type: 'error',
						message: '勾选中存在已通过或者已驳回状态'
					})
					return false
				}

				let ids = []
				this.dataListSelections.forEach(function(e) {
					ids.push(e.uuid)
				});
				this.$http({
					url: this.$http.adornUrl("/sale/saleApplyInto/examine"),
					method: "post",
					data: this.$http.adornData({
						uuids: ids
					})
				}).then(({
					data
				}) => {
					if(data && data.code === 0) {
						this.$message({
							message: "操作成功",
							type: "success",
							duration: 1500,
							onClose: () => {
								this.infoshow = false
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

				if(this.dataListSelections[0].examineStatus == 1) {
					this.$message({
						type: 'error',
						message: '该记录已审核通过'
					})
					return false
				} else if(this.dataListSelections[0].examineStatus == 2) {
					this.$message({
						type: 'error',
						message: '该记录已被驳回'
					})
					return false
				}

				this.noteshow = true
				this.examineNote = ''
			},
			submit() {
				this.$http({
					url: this.$http.adornUrl("/sale/saleApplyInto/refuseExamine"),
					method: "post",
					data: this.$http.adornData({
						uuids: [this.dataListSelections[0].uuid],
						examineNote: this.examineNote
					})
				}).then(({
					data
				}) => {
					if(data && data.code === 0) {
						this.$message({
							message: "操作成功",
							type: "success",
							duration: 1500,
							onClose: () => {
								this.infoshow = false
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
	.titlelist {
		overflow: hidden;
	}

	.titlelist li {
		width: 50%;
		float: left;
		min-height: 60px;
	}

	.titlelist li .title1 {
		width: 120px;
		display: inline-block;
	}

	.titlelist li .image {
		height: 100px;
		margin-bottom: 20px;
		border-radius: 8px;
	}
	.imgwarp {
		width: 100%;
	}
	.imgwarp .image {
		width: 100%;
	}
</style>
