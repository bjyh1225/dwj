<template>
	<el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
		<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
			<el-form-item label="用户名" prop="userName">
				<el-input v-model="dataForm.userName" placeholder="登录帐号"></el-input>
			</el-form-item>
			<el-form-item label="密码" prop="password" :class="{ 'is-required': !dataForm.id }">
				<el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
			</el-form-item>
			<el-form-item label="确认密码" prop="comfirmPassword" :class="{ 'is-required': !dataForm.id }">
				<el-input v-model="dataForm.comfirmPassword" type="password" placeholder="确认密码"></el-input>
			</el-form-item>
			<el-form-item label="邮箱" prop="email">
				<el-input v-model="dataForm.email" placeholder="邮箱"></el-input>
			</el-form-item>
			<el-form-item label="手机号" prop="mobile">
				<el-input v-model="dataForm.mobile" placeholder="手机号"></el-input>
			</el-form-item>
			<el-form-item label="角色" size="mini" prop="roleIdList">
				<el-checkbox-group v-model="dataForm.roleIdList">
					<el-checkbox v-for="role in roleList" :key="role.roleId" :label="role.roleId">{{ role.roleName }}</el-checkbox>
				</el-checkbox-group>
			</el-form-item>
			<el-form-item label="状态" size="mini" prop="status">
				<el-radio-group v-model="dataForm.status">
					<el-radio :label="0">禁用</el-radio>
					<el-radio :label="1">正常</el-radio>
				</el-radio-group>
			</el-form-item>
		</el-form>
		<span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
	</el-dialog>
</template>

<script>
	import { isEmail, isMobile } from '@/utils/validate'
	export default {
		data() {
			return {
				visible: false,
				roleList: [],
				dataForm: {
					id: 0,
					userName: '',
					password: '',
					comfirmPassword: '',
					salt: '',
					email: '',
					mobile: '',
					roleIdList: [],
					status: 1
				},
				dataRule: {
					userName: [{
						required: true,
						message: '用户名不能为空',
						trigger: 'blur'
					}],
					password: [{
						required: true,
						trigger: 'blur',
						message: '请输入密码'
					}],
					comfirmPassword: [{
						required: true,
						trigger: 'blur',
						message: '请输入密码'
					}],
					email: [{
						required: true,
						message: '邮箱不能为空',
						trigger: 'blur'
					}, {
						pattern: /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/,
						message: '请填写正确格式邮箱'
					}],

					mobile: [{
						required: true,
						message: '手机号不能为空',
						trigger: 'blur'
					}, {
						pattern: /^((0\d{2,4}-\d{7,8})|(1[1-9]\d{9}))$/,
						message: '请填写正确格式手机号码'
					}]
				}
			}
		},
		methods: {
			init(id) {
				this.dataForm.id = id || 0
				this.$http({
					url: this.$http.adornUrl('/sys/role/select'),
					method: 'get',
					params: this.$http.adornParams()
				}).then(({
					data
				}) => {
					this.roleList = data && data.code === 0 ? data.list : []
				}).then(() => {
					this.visible = true
					this.$nextTick(() => {
						this.$refs['dataForm'].resetFields()
					})
				}).then(() => {
					if(this.dataForm.id) {
						this.$http({
							url: this.$http.adornUrl(`/sys/user/info/${this.dataForm.id}`),
							method: 'get',
							params: this.$http.adornParams()
						}).then(({
							data
						}) => {
							if(data && data.code === 0) {
								this.dataForm.userName = data.user.username
								this.dataForm.salt = data.user.salt
								this.dataForm.email = data.user.email
								this.dataForm.mobile = data.user.mobile
								this.dataForm.roleIdList = data.user.roleIdList
								this.dataForm.status = data.user.status
							}
						})
					}
				})
			},
			// 表单提交
			dataFormSubmit() {
				this.$refs['dataForm'].validate((valid) => {
					if(valid) {
						this.$http({
							url: this.$http.adornUrl(`/sys/user/${!this.dataForm.id ? 'save' : 'update'}`),
							method: 'post',
							data: this.$http.adornData({
								'userId': this.dataForm.id || undefined,
								'username': this.dataForm.userName,
								'password': this.dataForm.password,
								'salt': this.dataForm.salt,
								'email': this.dataForm.email,
								'mobile': this.dataForm.mobile,
								'status': this.dataForm.status,
								'roleIdList': this.dataForm.roleIdList
							})
						}).then(({
							data
						}) => {
							if(data && data.code === 0) {
								this.$message({
									message: '操作成功',
									type: 'success',
									duration: 1500,
									onClose: () => {
										this.visible = false
										this.$emit('refreshDataList')
									}
								})
							} else {
								this.$message.error(data.msg)
							}
						})
					}
				})
			}
		}
	}
</script>