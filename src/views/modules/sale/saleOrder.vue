<template>
	<div class="mod-dict">
		<div class="card-wrapper">
			<h4>订单管理</h4>
			<div class="btns">
				<el-button type="primary" size="mini" @click="linHandle()">列选择</el-button>
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
					<el-select v-model="condition.orderListStatusDesc" size="mini" clearable placeholder="订单状态">
						<el-option label="待接单" value="waitAccept"></el-option>
						<el-option label="待核销" value="waitConsume"></el-option>
						<el-option label="完成" value="complete"></el-option>
						<el-option label="退款" value="refund"></el-option>
					</el-select>
				</el-form-item>
				<el-form-item>
					<el-date-picker v-model="condition.placeOrderTimes" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="至" start-placeholder="下单开始日期" end-placeholder="下单结束日期">
					</el-date-picker>
				</el-form-item>
				<el-form-item>
					<el-date-picker v-model="condition.preOrderTimes" size="mini" type="daterange" value-format="yyyy-MM-dd" range-separator="至" start-placeholder="预约开始日期" end-placeholder="预约结束日期">
					</el-date-picker>
				</el-form-item>
				
				<el-form-item>
					<el-input v-model="condition.orderName" placeholder="联系人姓名" size="mini" class="w120"></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="condition.orderTelephone" placeholder="联系电话" size="mini" class="w120"></el-input>
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
			<el-table-column prop="uuid" v-if="showlist.uuid" header-align="center" align="center" label="UUID">
			</el-table-column>
            <el-table-column prop="orderCode" v-if="showlist.orderCode" header-align="center" align="center" label="订单编码" min-width="145">
            </el-table-column>
			<el-table-column prop="sellerCode" v-if="showlist.sellerCode" header-align="center" align="center" label="商家编码"  min-width="85">
			</el-table-column>
			<el-table-column prop="sellerName" v-if="showlist.sellerName" header-align="center" align="center" label="商家名称" min-width="140">
			</el-table-column>
			<el-table-column prop="userId" v-if="showlist.userId" header-align="center" align="center" label="会员信息"  min-width="100">
                <template slot-scope="scope">
                    <span>{{ scope.row.userName }}</span><br>
                    <span>{{ scope.row.userId }}</span>
                </template>
			</el-table-column>
			<el-table-column prop="orderDate" v-if="showlist.orderDate" header-align="center" align="center" label="预约时间" :key="Math.random()"  min-width="130">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.orderDate).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.orderDate).toTimeString().substr(0, 5)}}</span>
				</template>
			</el-table-column>
			<el-table-column prop="orderName" v-if="showlist.orderName" header-align="center" align="left" label="预约信息"  min-width="200">
				<template slot-scope="scope">
					<span>姓名：{{ scope.row.orderName }}</span><br>
                    <span>电话：{{ scope.row.orderTelephone }}</span><br>
                    <span>人数：{{ scope.row.orderNumber }}</span>
				</template>
			</el-table-column>
			<el-table-column prop="orderStatus" v-if="showlist.orderStatus" header-align="center" align="center" label="状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.orderStatus == 0">未支付</span>
					<span v-else-if="scope.row.orderStatus == 10">待接单</span>
					<span v-else-if="scope.row.orderStatus == 20">待核销</span>
					<span v-else-if="scope.row.orderStatus == 25">待核销</span>
					<span v-else-if="scope.row.orderStatus == 30">已完成</span>
					<span v-else-if="scope.row.orderStatus == 40">已完成</span>
					<span v-else-if="scope.row.orderStatus == 50">已完成</span>
					<span v-else-if="scope.row.orderStatus == -10">已取消</span>
					<span v-else-if="scope.row.orderStatus == -20">已取消</span>
					<span v-else-if="scope.row.orderStatus == -30">退款</span>
					<span v-else-if="scope.row.orderStatus == -40">退款</span>
					<span v-else-if="scope.row.orderStatus == -50">退款</span>
				</template>
			</el-table-column>
			<el-table-column prop="orderPayType" v-if="showlist.orderPayType" header-align="center" align="center" label="支付"  min-width="60">
				<template slot-scope="scope">
					<span v-if="scope.row.orderPayType == 1"><span>零钱</span></span>
					<span v-else-if=" scope.row.orderPayType == 2">
						<span>微信</span>
					</span>
					<span v-else>
						<span>未知</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="goodsTotal" header-align="center" align="left" label="订单价格"  min-width="120">
                <template slot-scope="scope">
                    <span>原　价：{{scope.row.goodsTotal}}</span><br>
                    <span>会员价：{{scope.row.orderTotal}}</span>
                </template>
			</el-table-column>
			<el-table-column prop="seatCostAmount" v-if="showlist.seatCostAmount" header-align="center" align="center" label="餐位费"   min-width="65">
				<template slot-scope="scope">
					<span>{{scope.row.seatCostAmount}}</span>
				</template>
			</el-table-column>
            <el-table-column prop="sellerActualReceipt" header-align="center" align="center" label="商家收入">
                <template slot-scope="scope">
                    <span>{{scope.row.sellerActualReceipt}}</span>
                </template>
            </el-table-column>
			<el-table-column prop="paymentTime" v-if="showlist.paymentTime" header-align="center" align="center" label="支付时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.paymentTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.paymentTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.paymentTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="refundTime" v-if="showlist.refundTime" header-align="center" align="center" label="退款时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.refundTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.refundTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.refundTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="placeOrderTime" v-if="showlist.placeOrderTime" header-align="center" align="center" label="下单时间" :key="Math.random()"  min-width="130">
				<template slot-scope="scope">
					<span v-if="scope.row.placeOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.placeOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.placeOrderTime).toTimeString().substr(0, 5)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="acceptOrderTime" v-if="showlist.acceptOrderTime" header-align="center" align="center" label="接单时间" :key="Math.random()"  min-width="140">
				<template slot-scope="scope">
					<span v-if="scope.row.acceptOrderTime !== 0 ">
						<span>手动接单<br>{{new Date(scope.row.acceptOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.acceptOrderTime).toTimeString().substr(0, 5)}}</span>
					</span>
					<span v-else-if=" scope.row.autoAcceptOrderTime !== 0 ">
						<span>自动接单<br>{{new Date(scope.row.autoAcceptOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.autoAcceptOrderTime).toTimeString().substr(0, 5)}}</span>
					</span>
					<span v-else>未接单</span>
				</template>
			</el-table-column>
			<el-table-column prop="cashOrderTime" v-if="showlist.cashOrderTime" header-align="center" align="center" label="兑付时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.cashOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.cashOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.cashOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="confirmOrderTime" v-if="showlist.confirmOrderTime" header-align="center" align="center" label="确认时间" :key="Math.random()">
				<template slot-scope="scope">
					
					<span v-if="scope.row.confirmOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.confirmOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.confirmOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="fileOrderTime" v-if="showlist.fileOrderTime" header-align="center" align="center" label="完成时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.fileOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.fileOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.fileOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
<!--			<el-table-column prop="autoAcceptOrderTime" v-if="showlist.autoAcceptOrderTime" header-align="center" align="center" label="自动接单时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.autoAcceptOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.autoAcceptOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.autoAcceptOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>-->
			<el-table-column prop="cancelOrderTime" v-if="showlist.cancelOrderTime" header-align="center" align="center" label="取消时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.cancelOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.cancelOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.cancelOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="cancelPaymentOrderTime" v-if="showlist.cancelPaymentOrderTime" header-align="center" align="center" label="取消并退款时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.cancelPaymentOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.cancelPaymentOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.cancelPaymentOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="consumerConsultCancelApplyTime" v-if="showlist.consumerConsultCancelApplyTime" header-align="center" align="center" label="消费者协议取消申请时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.consumerConsultCancelApplyTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.consumerConsultCancelApplyTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.consumerConsultCancelApplyTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="consumerConsultCancelAgreeTime" v-if="showlist.consumerConsultCancelAgreeTime" header-align="center" align="center" label="消费者协议取消同意时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.consumerConsultCancelAgreeTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.consumerConsultCancelAgreeTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.consumerConsultCancelAgreeTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="consumerConsultCancelDisagreeTime" v-if="showlist.consumerConsultCancelDisagreeTime" header-align="center" align="center" label="消费者协议取消拒绝时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.consumerConsultCancelDisagreeTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.consumerConsultCancelDisagreeTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.consumerConsultCancelDisagreeTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="consumerConsultCancelApplyReason" v-if="showlist.consumerConsultCancelApplyReason" header-align="center" align="center" label="消费者协议取消申请原因" :key="Math.random()">
			</el-table-column>
			<el-table-column prop="consumerConsultCancelApplyReply" v-if="showlist.consumerConsultCancelApplyReply" header-align="center" align="center" label="消费者协议取消申请答复" :key="Math.random()">
			</el-table-column>
			<el-table-column prop="refuseOrderTime" v-if="showlist.refuseOrderTime" header-align="center" align="center" label="拒单时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.refuseOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.refuseOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.refuseOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="autoRefuseOrderTime" v-if="showlist.autoRefuseOrderTime" header-align="center" align="center" label="自动拒单时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.autoRefuseOrderTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.autoRefuseOrderTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.autoRefuseOrderTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="sellerConsultCancelApplyTime" v-if="showlist.sellerConsultCancelApplyTime" header-align="center" align="center" label="商家协议取消申请时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.sellerConsultCancelApplyTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.sellerConsultCancelApplyTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.sellerConsultCancelApplyTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="sellerConsultCancelAgreeTime" v-if="showlist.sellerConsultCancelAgreeTime" header-align="center" align="center" label="商家协议取消同意时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.sellerConsultCancelAgreeTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.sellerConsultCancelAgreeTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.sellerConsultCancelAgreeTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="sellerConsultCancelDisagreeTime" v-if="showlist.sellerConsultCancelDisagreeTime" header-align="center" align="center" label="商家协议取消拒绝时间" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.sellerConsultCancelDisagreeTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.sellerConsultCancelDisagreeTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.sellerConsultCancelDisagreeTime).toTimeString().substr(0, 8)}}</span>
					</span>
				</template>
			</el-table-column>
			<el-table-column prop="sellerConsultCancelApplyReason" v-if="showlist.sellerConsultCancelApplyReason" header-align="center" align="center" label="商家协议取消申请原因">
			</el-table-column>
			<el-table-column prop="sellerConsultCancelApplyReply" v-if="showlist.sellerConsultCancelApplyReply" header-align="center" align="center" label="商家协议取消申请答复">
			</el-table-column>
			<el-table-column prop="occupyStockStatus" v-if="showlist.occupyStockStatus" header-align="center" align="center" label="库存占用状态">
			</el-table-column>
			<el-table-column prop="cancelStockStatus" v-if="showlist.cancelStockStatus" header-align="center" align="center" label="是否自动取消库存预留">
			</el-table-column>

			<el-table-column prop="sellerAccountType" v-if="showlist.sellerAccountType" header-align="center" align="center" label="商家账户类别">
			</el-table-column>
			<el-table-column prop="sellerAccount" v-if="showlist.sellerAccount" header-align="center" align="center" label="商家账户">
			</el-table-column>

<!--			<el-table-column prop="goodsTotal" v-if="showlist.goodsTotal" header-align="center" align="center" label="商品总价">
			</el-table-column>-->
			<el-table-column prop="goodsClassNumber" v-if="showlist.goodsClassNumber" header-align="center" align="center" label="商品类别数量">
			</el-table-column>
			<el-table-column prop="goodsNumber" v-if="showlist.goodsNumber" header-align="center" align="center" label="商品数量">
			</el-table-column>

			<el-table-column prop="orderNote" v-if="showlist.orderNote" header-align="center" align="center" label="订单备注">
			</el-table-column>
			<el-table-column prop="orderEvaluate" v-if="showlist.orderEvaluate" header-align="center" align="center" label="订单评价">
			</el-table-column>
			<el-table-column prop="industryTypeCode" v-if="showlist.industryTypeCode" header-align="center" align="center" label="行业类别编码">
			</el-table-column>
			<el-table-column prop="industryTypeName" v-if="showlist.industryTypeName" header-align="center" align="center" label="行业类别名称">
			</el-table-column>

			<el-table-column prop="serveTime" v-if="showlist.serveTime" header-align="center" align="center" label="营业时间:字符串">
			</el-table-column>
			<el-table-column prop="serveStatus" v-if="showlist.serveStatus" header-align="center" align="center" label="营业状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.serveStatus == 0">营业中</span>
					<span v-else-if="scope.row.serveStatus == 1">未营业</span>
				</template>
			</el-table-column>
			<el-table-column prop="lowestConsume" v-if="showlist.lowestConsume" header-align="center" align="center" label="人均消费">
			</el-table-column>
			<el-table-column prop="validTime" v-if="showlist.validTime" header-align="center" align="center" label="有效期默认值" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.validTime == 0"></span>
					<span v-else>
						<span>{{new Date(scope.row.validTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.validTime).toTimeString().substr(0, 8)}}</span>
					</span>
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
			<el-table-column prop="stockQuantity" v-if="showlist.stockQuantity" header-align="center" align="center" label="库存默认值">
			</el-table-column>
			<el-table-column prop="occupyStockType" v-if="showlist.occupyStockType" header-align="center" align="center" label="库存扣除类型">
			</el-table-column>
			<el-table-column prop="acceptStatus" v-if="showlist.acceptStatus" header-align="center" align="center" label="是否自动接单">
			</el-table-column>
			<el-table-column prop="acceptDelay" v-if="showlist.acceptDelay" header-align="center" align="center" label="自动接单延迟时间">
			</el-table-column>
			<el-table-column prop="returnStockStatus" v-if="showlist.returnStockStatus" header-align="center" align="center" label="超时未支付是否取消预留">
			</el-table-column>
			<el-table-column prop="overtimeLength" v-if="showlist.overtimeLength" header-align="center" align="center" label="超时设置">
			</el-table-column>
			<el-table-column prop="billStatus" v-if="showlist.billStatus" header-align="center" align="center" label="是否提供发票">
			</el-table-column>
			<el-table-column prop="billOpenStatus" v-if="showlist.billOpenStatus" header-align="center" align="center" label="是否已开具发票" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.billOpenStatus == 0">未开具</span>
					<span v-else-if="scope.row.billOpenStatus == 1">已开具</span>
				</template>
			</el-table-column>
			<el-table-column prop="urgePaymentStatus" v-if="showlist.urgePaymentStatus" header-align="center" align="center" label="是否已催付" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.urgePaymentStatus == 0">未催付</span>
					<span v-else-if="scope.row.urgePaymentStatus == 1">已催付</span>
				</template>
			</el-table-column>
			<el-table-column prop="arriveAddressStatus" v-if="showlist.arriveAddressStatus" header-align="center" align="center" label="是否已到店" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.arriveAddressStatus == 0">没到店</span>
					<span v-else-if="scope.row.arriveAddressStatus == 1">已到店</span>
				</template>
			</el-table-column>
			<el-table-column prop="cashCode" v-if="showlist.cashCode" header-align="center" align="center" label="核销码">
			</el-table-column>
			<el-table-column prop="paymentCode" v-if="showlist.paymentCode" header-align="center" align="center" label="支付单号">
			</el-table-column>
			<el-table-column prop="refundCode" v-if="showlist.refundCode" header-align="center" align="center" label="退款单号">
			</el-table-column>
			<el-table-column prop="deleteStatus" v-if="showlist.deleteStatus" header-align="center" align="center" label="删除状态" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.deleteStatus == 0">正常</span>
					<span v-else-if="scope.row.deleteStatus == 1">已删除</span>
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
			<el-table-column prop="createUserType" v-if="showlist.createUserType" header-align="center" align="center" label="创建人类型" :key="Math.random()">
				<template slot-scope="scope">
					<span v-if="scope.row.createUserType == 0">平台</span>
					<span v-else-if="scope.row.createUserType == 1">商家</span>
					<span v-else-if="scope.row.createUserType == 1">消费者</span>
				</template>
			</el-table-column>
			<el-table-column prop="createUser" v-if="showlist.createUser" header-align="center" align="center" label="创建人">
			</el-table-column>
			<el-table-column prop="createTime" v-if="showlist.createTime" header-align="center" align="center" label="创建时间" :key="Math.random()">
				<template slot-scope="scope">
					<span>{{new Date(scope.row.createTime).toLocaleDateString().replace(/\//g, "-")}}</span>&nbsp;<span>{{new Date(scope.row.createTime).toTimeString().substr(0, 8)}}</span>
				</template>
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
		</el-table>
		<div class="btn-warper">
			<el-button type="primary" size="mini" @click="showinfo">详情</el-button>
			<!--<el-button type="primary" size="mini">催付</el-button>
			<el-button type="primary" size="mini">接单</el-button>
			<el-button type="primary" size="mini">商家取消</el-button>
			<el-button type="primary" size="mini">拒单</el-button>
			<el-button type="primary" size="mini">同意取消订单</el-button>
			<el-button type="primary" size="mini">申请拒单</el-button>
			<el-button type="primary" size="mini">订单打印</el-button>-->
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

		<el-dialog title="详情" :close-on-click-modal="false" width="1640px" :visible.sync="infoshow" :append-to-body="false" class="order">
			<el-row>
				<el-col :span="24">
					<el-card class="box-card">
						<ul class="titlelist">
							<li>
								<span>商家：{{item.sellerName}}</span>
							</li>
							<li>
								<span>营业时间：{{item.serveTime}}</span>
							</li>
							<li>
								<span>订单编号：{{item.orderCode}}</span>
							</li>
							<li>
								<span>订单总价：{{item.orderTotal}}</span>
							</li>
							<li>
								<span>联系电话：{{item.orderTelephone}}</span>
							</li>
							<li>
								<span>订单姓名：{{item.orderName}}</span>
							</li>
							<li>
								<span>用户姓名：{{item.userName}}</span>
							</li>
							<li>
								<span>用户账户：{{item.userAccount}}</span>
							</li>
						</ul>
					</el-card>
				</el-col>
			</el-row>

			<el-row>
				<el-col :span="18">
					<el-card class="box-card" style="margin-bottom: 20px;">
						<ul class="infolist">
							<li>
								<span>订单状态：</span>
								<span v-if="item.orderStatus == 0">未支付</span>
								<span v-else-if="item.orderStatus == 10">已支付</span>
								<span v-else-if="item.orderStatus == 20">已接单</span>
								<span v-else-if="item.orderStatus == 25">已自动接单</span>
								<span v-else-if="item.orderStatus == 30">商家已兑付</span>
								<span v-else-if="item.orderStatus == 40">已确认</span>
								<span v-else-if="item.orderStatus == 50">已完成</span>
								<span v-else-if="item.orderStatus == -10">已取消</span>
								<span v-else-if="item.orderStatus == -20">已自动取消</span>
								<span v-else-if="item.orderStatus == -30">已取消并退款</span>
								<span v-else-if="item.orderStatus == -40">已拒单并退款</span>
								<span v-else-if="item.orderStatus == -50">协议取消并退款</span>
								<span v-else-if="item.orderStatus == -60">协议拒单并退款</span>
							</li>
							<li>
								<span>用户编号：</span><span>{{item.userId}}</span>
							</li>
							<li>
								<span>用户账户类别：</span>
								<span v-if="item.userAccountType == 0">微信</span>
								<span v-else-if="item.userAccountType == 1">支付宝</span>
								<span v-else-if="item.userAccountType == 2">银行卡</span>
							</li>
							<li>
								<span>商家账户类别：</span>
								<span v-if="item.sellerAccountType == 0">微信</span>
								<span v-else-if="item.sellerAccountType == 1">支付宝</span>
								<span v-else-if="item.sellerAccountType == 2">银行卡</span>
							</li>
							<li>
								<span>商家账户：</span><span>{{item.sellerAccount}}</span>
							</li>
							<li>
								<span>商品总价：</span><span>{{item.goodsTotal}}</span>
							</li>
							<li>
								<span>优惠券金额：</span><span>{{item.couponTotal}}</span>
							</li>
							<li>
								<span>优惠券编码：</span><span>{{item.couponCode}}</span>
							</li>
							<li>
								<span>订单联系电话：</span><span>{{item.orderTelephone}}</span>
							</li>
							<li v-if="item.orderDate != 0">
								<span>订单预约日期：</span><span>{{new Date(item.orderDate).toLocaleDateString().replace(/\//g, "-")}}</span>
							</li>
							<li v-if="item.orderTime != 0">
								<span>订单预约时间：</span><span>{{new Date(item.orderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.orderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li>
								<span>订单预约人数：</span><span>{{item.orderNumber}}</span>
							</li>
							<li>
								<span>订单备注：</span><span>{{item.orderNote}}</span>
							</li>

							<li>
								<span>反馈星级：</span><span>{{item.feedbackStar}}</span>
							</li>
							<li>
								<span>反馈标签：</span><span>{{item.feedbackLable}}</span>
							</li>
						</ul>
					</el-card>
					<el-card class="box-card">
						<ul class="infolist">
							<li v-if="item.paymentTime != 0">
								<span>支付时间：</span><span>{{new Date(item.paymentTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.paymentTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li>
								<span>支付单号：</span><span>{{item.paymentCode}}</span>
							</li>
							<li v-if="item.cancelPaymentOrderTime != 0">
								<span>退款时间：</span><span>{{new Date(item.cancelPaymentOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.cancelPaymentOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.placeOrderTime != 0">
								<span>下单时间：</span><span>{{new Date(item.placeOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.placeOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.acceptOrderTime != 0">
								<span>接单时间：</span><span>{{new Date(item.acceptOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.acceptOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.autoAcceptOrderTime != 0">
								<span>自动接单时间：</span><span>{{new Date(item.autoAcceptOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.autoAcceptOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li>
								<span>是否已到店：</span>
								<span v-if="item.arriveAddressStatus == 0">没到店</span>
								<span v-else-if="item.userAccountType == 1">已到店</span>
							</li>
							<li v-if="item.cashOrderTime != 0">
								<span>核销时间：</span><span>{{new Date(item.cashOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.cashOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.confirmOrderTime != 0">
								<span>确认时间：</span><span>{{new Date(item.confirmOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.confirmOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.fileOrderTime != 0">
								<span>完成时间：</span><span>{{new Date(item.fileOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.fileOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.cancelOrderTime != 0">
								<span>消费者取消时间：</span><span>{{new Date(item.cancelOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.cancelOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.cancelPaymentOrderTime != 0">
								<span>取消并退款时间：</span><span>{{new Date(item.cancelPaymentOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.cancelPaymentOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li>
								<span>是否已催付：</span>
								<span v-if="item.urgePaymentStatus == 0">未催付</span>
								<span v-else-if="item.urgePaymentStatus == 1">已催付</span>
							</li>
							<li v-if="item.urgePaymentTime != 0">
								<span>商家催付时间：</span><span>{{new Date(item.urgePaymentTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.urgePaymentTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.sellerAncelOrderTime != 0">
								<span>商家取消时间：</span><span>{{new Date(item.sellerAncelOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.sellerAncelOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
						</ul>
					</el-card>
				</el-col>
				<el-col :span="6">
					<el-card class="box-card">
						<ul class="rightlist">
							<li v-if="item.consumerConsultCancelApplyTime == 0">
								<span>暂无退款相关流程</span>
							</li>
							<li v-if="item.refuseOrderTime != 0">
								<span>拒单时间：</span><span>{{new Date(item.refuseOrderTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.refuseOrderTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.consumerConsultCancelApplyTime != 0">
								<span>消费者申请取消时间：</span><span>{{new Date(item.consumerConsultCancelApplyTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.consumerConsultCancelApplyTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.consumerConsultCancelApplyTime != 0">
								<span>消费者申请取消的缘由：</span><span>{{item.consumerConsultCancelApplyReason}}</span>
							</li>
							<li v-if="item.consumerConsultCancelAgreeTime != 0">
								<span>商家同意取消时间：</span><span>{{new Date(item.consumerConsultCancelAgreeTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.consumerConsultCancelAgreeTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.consumerConsultCancelDisagreeTime != 0">
								<span>商家拒绝取消时间：</span><span>{{new Date(item.consumerConsultCancelDisagreeTime).toLocaleDateString().replace(/\//g, "-")}}&nbsp;{{new Date(item.consumerConsultCancelDisagreeTime).toTimeString().substr(0, 8)}}</span>
							</li>
							<li v-if="item.consumerConsultCancelAgreeTime != 0 || item.consumerConsultCancelAgreeTime != 0">
								<span>商家同意或拒绝的答复：</span><span>{{item.sellerConsultCancelApplyReply}}</span>
							</li>
						</ul>
					</el-card>
				</el-col>
			</el-row>

			<el-table :data="infodatalist" border v-loading="dataListLoading" style="width: 100%;">
				<el-table-column prop="goodsFullName" header-align="center" align="center" label="商品名称">
				</el-table-column>
				<el-table-column prop="goodsUnit" header-align="center" align="center" label="单位">
				</el-table-column>
				<el-table-column prop="spec" header-align="center" align="center" label="规格">
					<template slot-scope="scope">
						<span v-if="scope.row.spec == 'default'">默认规格</span>
						<span v-else>{{scope.row.spec}}</span>
					</template>
				</el-table-column>
				<el-table-column prop="nonMemberPrice" header-align="center" align="center" label="价格">
				</el-table-column>
				<el-table-column prop="memberPrice" header-align="center" align="center" label="会员价">
				</el-table-column>
				<el-table-column prop="goodsQuantity" header-align="center" align="center" label="数量">
				</el-table-column>
				<el-table-column prop="goodsTotalPrice" header-align="center" align="center" label="商品总价">
				</el-table-column>
			</el-table>
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
					orderListStatusDesc: '',
					goodsTypeCode: '',
					listStatus: '',
					placeOrderTimes: [],
					preOrderTimes: [],
					orderTelephone: '',
					orderName: ''
				},
				
				othercondition: {
					sort: 'placeOrderTime'
				},
				AddressList: [],
				dataList: [],
				infodatalist: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				addOrUpdateVisible: false,
				types: [],
				sellers: [],
				options: [{
						name: 'uuid',
						value: 'UUID'
					},
					{
						name: 'orderCode',
						value: '订单编码'
					},
					{
						name: 'orderStatus',
						value: '订单状态'
					},
					{
						name: 'paymentTime',
						value: '支付时间'
					},
					{
						name: 'refundTime',
						value: '退款时间'
					},
					{
						name: 'placeOrderTime',
						value: '下单时间'
					},
					{
						name: 'acceptOrderTime',
						value: '接单时间'
					},
					{
						name: 'cashOrderTime',
						value: '兑付时间'
					},

					{
						name: 'confirmOrderTime',
						value: '确认时间'
					},
					{
						name: 'fileOrderTime',
						value: '完成时间'
					},
					{
						name: 'autoAcceptOrderTime',
						value: '自动接单时间'
					},
					{
						name: 'cancelOrderTime',
						value: '取消时间'
					},
					{
						name: 'cancelPaymentOrderTime',
						value: '取消并退款时间'
					},
					{
						name: 'consumerConsultCancelApplyTime',
						value: '消费者协议取消申请时间'
					},
					{
						name: 'consumerConsultCancelAgreeTime',
						value: '消费者协议取消同意时间'
					},
					{
						name: 'consumerConsultCancelDisagreeTime',
						value: '消费者协议取消拒绝时间'
					},
					{
						name: 'consumerConsultCancelApplyReason',
						value: '消费者协议取消申请原因'
					},

					{
						name: 'consumerConsultCancelApplyReply',
						value: '消费者协议取消申请答复'
					},
					{
						name: 'refuseOrderTime',
						value: '拒单时间'
					},
					{
						name: 'autoRefuseOrderTime',
						value: '自动拒单时间'
					},
					{
						name: 'sellerConsultCancelApplyTime',
						value: '商家协议取消申请时间'
					},
					{
						name: 'sellerConsultCancelAgreeTime',
						value: '商家协议取消同意时间'
					},
					{
						name: 'sellerConsultCancelDisagreeTime',
						value: '商家协议取消拒绝时间'
					},
					{
						name: 'sellerConsultCancelApplyReason',
						value: '商家协议取消申请原因'
					},
					{
						name: 'sellerConsultCancelApplyReply',
						value: '商家协议取消申请答复'
					},
					{
						name: 'occupyStockStatus',
						value: '库存占用状态'
					},
					{
						name: 'cancelStockStatus',
						value: '是否自动取消库存'
					},

					{
						name: 'userId',
						value: '用户编号'
					},
					{
						name: 'userName',
						value: '用户姓名'
					},
					{
						name: 'userTelephone',
						value: '用户电话'
					},
					{
						name: 'userAccountType',
						value: '用户账户类别'
					},

					{
						name: 'userAccount',
						value: '用户账户'
					},
					{
						name: 'sellerAccountType',
						value: '商家账户类别'
					},
					{
						name: 'sellerAccount',
						value: '商家账户'
					},
					{
						name: 'orderTotal',
						value: '订单总价'
					},
					{
						name: 'goodsTotal',
						value: '商品总价'
					},
					{
						name: 'goodsClassNumber',
						value: '商品类别数量'
					},
					{
						name: 'goodsNumber',
						value: '商品数量'
					},
					{
						name: 'orderName',
						value: '订单姓名'
					},
					{
						name: 'orderTelephone',
						value: '订单电话'
					},
					{
						name: 'orderDate',
						value: '订单预约日期'
					},
					{
						name: 'orderTime',
						value: '订单预约时间'
					},
					{
						name: 'orderNumber',
						value: '订单预约人数'
					},
					{
						name: 'orderNote',
						value: '订单备注'
					},
					{
						name: 'orderEvaluate',
						value: '订单评价'
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
						name: 'sellerCode',
						value: '商家编码'
					},
					{
						name: 'sellerName',
						value: '商家名称'
					},
					{
						name: 'displayLable',
						value: '商家标签'
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
						name: 'validTime',
						value: '有效期默认值'
					},
					{
						name: 'stockEnableStatus',
						value: '库存启用状态'
					},
					{
						name: 'stockDays',
						value: '库存天数0天'
					},
					{
						name: 'stockQuantity',
						value: '库存默认值'
					},
					{
						name: 'occupyStockType',
						value: '库存扣除类型'
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
						name: 'billStatus',
						value: '是否提供发票'
					},
					{
						name: 'billOpenStatus',
						value: '是否已开具发票'
					},
					{
						name: 'urgePaymentStatus',
						value: '是否已催付'
					},
					{
						name: 'arriveAddressStatus',
						value: '是否已到店'
					},
					{
						name: 'cashCode',
						value: '核销码'
					},
					{
						name: 'paymentCode',
						value: '支付单号'
					},
					{
						name: 'refundCode',
						value: '退款单号'
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
						name: 'createUserType',
						value: '创建人类型'
					},
					{
						name: 'createUser',
						value: '上传人'
					},
					{
						name: 'createTime',
						value: '创建时间'
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
				],

				showlist: {
					uuid: false,
					orderCode: true,
					orderStatus: true,
					paymentTime: false,
					refundTime: false,
					placeOrderTime: true,
					acceptOrderTime: true,
					cashOrderTime: false,
					confirmOrderTime: false,
					fileOrderTime: false,
					autoAcceptOrderTime: false,
					cancelOrderTime: false,
					cancelPaymentOrderTime: false,
					consumerConsultCancelApplyTime: false,
					consumerConsultCancelAgreeTime: false,
					consumerConsultCancelDisagreeTime: false,
					consumerConsultCancelApplyReason: false,
					consumerConsultCancelApplyReply: false,
					refuseOrderTime: false,
					autoRefuseOrderTime: false,
					seatCostAmount: true,
					sellerConsultCancelApplyTime: false,
					sellerConsultCancelAgreeTime: false,
					sellerConsultCancelDisagreeTime: false,
					sellerConsultCancelApplyReason: false,
					sellerConsultCancelApplyReply: false,
					occupyStockStatus: false,
					cancelStockStatus: false,
					userId: true,
					userName: true,
					userTelephone: true,
					userAccountType: true,
					orderPayType:true,
					userAccount: false,
					sellerAccountType: false,
					sellerAccount: false,
					orderTotal: true,
					goodsTotal: false,
					sellerActualReceipt:true,
					goodsClassNumber: false,
					goodsNumber: false,
					orderName: true,
					orderTelephone: true,
					orderDate: true,
					orderTime: false,
					orderNumber: true,
					orderNote: false,
					orderEvaluate: false,
					industryTypeCode: false,
					industryTypeName: false,
					sellerCode: true,
					sellerName: true,
					displayLable: false,
					serveTime: false,
					serveStatus: false,
					lowestConsume: false,
					validTime: false,
					stockEnableStatus: false,
					stockDays: false,
					stockQuantity: false,
					occupyStockType: false,
					acceptStatus: false,
					acceptDelay: false,
					returnStockStatus: false,
					overtimeLength: false,
					billStatus: false,
					billOpenStatus: false,
					urgePaymentStatus: false,
					arriveAddressStatus: false,
					cashCode: true,
					paymentCode: false,
					refundCode: false,
					deleteStatus: false,
					note: false,
					status: false,
					createUserType: false,
					createUser: false,
					createTime: false,
					updateUserType: false,
					updateUser: false,
					updateTime: false

				},

				linVisible: false,
				isIndeterminate: false,
				checkAll: false,
				checkList: ['sellerName', 'orderDate', 'orderCode', 'orderStatus', 'orderTotal', 'orderNumber', 'cashCode', 'cancelOrderTime', 'refuseOrderTime', 'userName', 'userTelephone', 'orderName', 'orderTelephone', 'placeOrderTime'],
				emptyDate: {},
				infoshow: false,
				item: {}
			};
		},
		activated() {
			this.getDataList();
			this.getTypes()
			this.AddressList = jsondata
			this.emptyDate = this.dataForm
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
					orderListStatusDesc: '',
					goodsTypeCode: '',
					listStatus: '',
					placeOrderTimes: [],
					preOrderTimes: [],
					userName: '',
					orderName: ''
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

			// 获取数据列表
			getDataList() {
				this.dataListLoading = true;
				
				if(this.condition.placeOrderTimes!=null&&this.condition.placeOrderTimes.length > 0){
					this.condition.placeOrderTimes[0] = new Date(this.condition.placeOrderTimes[0]).getTime()
					this.condition.placeOrderTimes[1] = new Date(this.condition.placeOrderTimes[1]).getTime()
				}
				
				if(this.condition.preOrderTimes!=null&&this.condition.preOrderTimes.length > 0){
					this.condition.preOrderTimes[0] = new Date(this.condition.preOrderTimes[0]).getTime()
					this.condition.preOrderTimes[1] = new Date(this.condition.preOrderTimes[1]).getTime()
				}
				
				
				this.$http({
					url: this.$http.adornUrl("/sale/saleOrder/list"),
					method: "post",
					data: this.$http.adornData({
						page: this.pageIndex,
						size: this.pageSize,
						condition: this.condition,
						otherCondition: this.othercondition
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
			showinfo() {
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
				this.infoshow = true
				this.item = this.dataListSelections[0]
				let uuid = this.item.uuid

				this.$http({
					url: this.$http.adornUrl("/sale/saleOrder/info/" + uuid),
					method: "get"
				}).then(({
					data
				}) => {
					this.infodatalist = data.saleOrder.itemList
				});
			}
		}
	};
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
	.mod-home {
		line-height: 1.5;
	}
	
	.titlelist {
		overflow: hidden;
		padding-left: 10px;
		margin: 0;
		display: flex;
		justify-content: space-around;
		li {
			float: left;
			margin-right: 10px;
			height: 28px;
			line-height: 28px;
			span:nth-of-type(1) {
				font-weight: 600;
			}
			span:nth-of-type(2) {
				margin-left: 10px;
			}
		}
	}
	
	.el-card {
		margin: 10px;
	}
	
	.infolist {
		padding-left: 10px;
		margin: 0;
		overflow: hidden;
		li {
			float: left;
			width: 33%;
			height: 28px;
			line-height: 28px;
			span:nth-of-type(1) {
				font-weight: 600;
				height: 28px;
				line-height: 28px;
				display: block;
				float: left;
			}
			span:nth-of-type(2) {
				height: 28px;
				line-height: 28px;
				display: block;
				float: left;
				width: 160px;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
			}
		}
	}
	
	.rightlist {
		padding-left: 10px;
		margin: 0;
		overflow: hidden;
		li {
			float: left;
			height: 28px;
			line-height: 28px;
			span:nth-of-type(1) {
				font-weight: 600;
				height: 28px;
				line-height: 28px;
				display: block;
				float: left;
			}
			span:nth-of-type(2) {
				height: 28px;
				line-height: 28px;
				display: block;
				float: left;
				width: 180px;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
			}
		}
	}
</style>