<template>
	<div>
        <el-aside style="border-right: 1px solid rgb(238, 238, 238)">
            <el-form ref="form" id="trade" :model="form">
                <el-form-item label="">
                    <el-input v-model="theinstrumentId" clearable></el-input>
                </el-form-item>
                <el-form-item label="手数">
                    <el-input-number v-model="form.volume" />
                </el-form-item>
                <el-form-item label="价格">
                    <el-input-number :step="priceStep" v-model="theLastPrice" />
                </el-form-item>
                <el-row>
                    <el-col :span="12">
                        <el-button plain @click="setOrder('BUY','OPEN')">买开</el-button>
                    </el-col>
                    <el-col :span="12">
                        <el-button plain @click="setOrder('SELL','OPEN')">卖开</el-button>
                    </el-col>
                    <el-col :span="12">
                        <el-button plain @click="setOrder('BUY','CLOSE')">买平</el-button>
                    </el-col>
                    <el-col :span="12">
                        <el-button plain @click="setOrder('SELL','CLOSE')">卖平</el-button>
                    </el-col>
                </el-row>
            </el-form>
        </el-aside>
        <el-main>
            <el-tabs type="border-card">
                <el-tab-pane label="账户" name="first">
                    <mTable id="table-four" height="150" :tableData="account" :tableHeader="accountHeader" @cellClick="cellClick" />
                </el-tab-pane>
                <el-tab-pane label="持仓" name="third">
                    <mTable id="table-two" height="150" :tableData="positions" :tableHeader="positionHeader" @cellClick="cellClick" />
                </el-tab-pane>
                <el-tab-pane label="委托单" name="second">
                    <mTable id="table-one" :tableData="orders" :tableHeader="orderHeader" @cellClick="cellClick" @cellDblclick="cellDblclick"
                            height="150" />
                </el-tab-pane>
                <el-tab-pane label="成交记录" name="fourth">
                    <mTable id="table-three" height="150" :tableData="trades" :tableHeader="tradeHeader" @cellClick="cellClick" />
                </el-tab-pane>
            </el-tabs>
        </el-main>

	</div>
</template>