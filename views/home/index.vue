<template>
    <el-row class="home" :gutter="20">
        <el-col :span="8" style="margin-top: 20px;">
            <el-card shadow="hover">
                <div class="user">
                    <img :src="userImg"/>
                    <div class="userinfo">
                        <p class="name">Admin</p>
                        <p class="access">超级管理员</p>
                    </div>
                </div>
                <div class="login-info">
                    <p>上次登陆时间：<span>2023-4-9</span></p>
                    <p>上次登陆地点：<span>东莞</span></p>
                </div>
            </el-card>
            <el-card style="margin-top: 20px;height: 460px;">
                <el-table :data="tableData">
                    <el-table-column v-for="(val,key) in tableLabel"
                    :key="key"
                    :prop="key"
                    :label="val"
                    >
                </el-table-column>
                </el-table>
            </el-card>
        </el-col>
        <el-col style="margin-top: 20px;" :span="16">
            <div class="num">
                <el-card v-for="item in countData" :key="item.name" :body-style="{display:'flex',padding:0
            }">
                    <i class="icon" :class="`el-icon-${item.icon}`" :style="{background:item.color}"></i>
                    <div class="detail">
                        <p class="price">￥{{ item.value }}</p>
                        <p class="desc">{{ item.name }}</p>
                    </div>
                </el-card>
            </div>
            <el-card style="height: 280px;">
                <!-- <div style="height: 280px;" ref="echarts"></div> -->
                <Echart :chartData="echartData.order" style="height: 280px;" />
            </el-card>
            <div class="graph">
                <el-card style="height: 260px;">
                    <!-- <div style="height: 240px;" ref="userecharts"></div> -->
                    <Echart :chartData="echartData.user" style="height: 240px;" />
                </el-card>
                <el-card style="height: 260px;">
                    <!-- <div style="height: 240px;" ref="videoecharts"></div> -->
                    <Echart :chartData="echartData.video" :isAxisChart="false" style="height: 240px;" />
                </el-card>
            </div>
        </el-col>
    </el-row>
</template>
<script>
import { getData } from '../../api/data.js'
// import * as echarts from 'echarts'
import Echart from '../../src/components/Echarts.vue'
export default {
    name:'home',
    components:{
        Echart
    },
    data(){
        return{
            userImg:require('../../src/assets/images/user.png'),
            tableData: [],
            tableLabel:{
                name:'课程',
                todayBuy:'今日购买',
                monthBuy:'本月购买',
                totalBuy:'总购买'
            },
            countData: [
                {
                name: "今日支付订单",
                value: 1234,
                icon: "success",
                color: "#2ec7c9",
                },
                {
                name: "今日收藏订单",
                value: 210,
                icon: "star-on",
                color: "#ffb980",
                },
                {
                name: "今日未支付订单",
                value: 1234,
                icon: "s-goods",
                color: "#5ab1ef",
                },
                {
                name: "本月支付订单",
                value: 1234,
                icon: "success",
                color: "#2ec7c9",
                },
                {
                name: "本月收藏订单",
                value: 210,
                icon: "star-on",
                color: "#ffb980",
                },
                {
                name: "本月未支付订单",
                value: 1234,
                icon: "s-goods",
                color: "#5ab1ef",
                },
      ],
            echartData:{
                order:{
                    xData:[],
                    series:[]
                },
                user:{
                    xData:[],
                    series:[]
                },
                video:{
                    series:[]
                }
            }
        }
    },
    mounted(){
        getData().then(res=>{
            const {code,data}=res.data
            if(code===20000){
                this.tableData=data.tableData
                const order = data.orderData
                const xData=order.date
                const keyArray= Object.keys(order.data[0])
                const series=[]
                keyArray.forEach(key=>{
                    series.push({
                        name:key,
                        data:order.data.map(item=>item[key]),
                        type:'line'
                    })
                })
                // const option={
                //     xAxis:{
                //         data:xData
                //     },
                //     yAxis:{},
                //     legend:{
                //         data:keyArray
                //     },
                //     series
                // }
                this.echartData.order.xData=xData
                this.echartData.order.series=series
                // const E=echarts.init(this.$refs.echarts)
                // E.setOption(option)
                //用户图
                // const userOption={
                //             legend: {
                //     // 图例文字颜色
                //     textStyle: {
                //     color: "#333",
                //     },
                // },
                // grid: {
                //     left: "20%",
                // },
                // // 提示框
                // tooltip: {
                //     trigger: "axis",
                // },
                // xAxis: {
                //     type: "category", // 类目轴
                //     data: data.userData.map(item=>item.date),
                //     axisLine: {
                //     lineStyle: {
                //         color: "#17b3a3",
                //     },
                //     },
                //     axisLabel: {
                //     interval: 0,
                //     color: "#333",
                //     },
                // },
                // yAxis: [
                //     {
                //     type: "value",
                //     axisLine: {
                //         lineStyle: {
                //         color: "#17b3a3",
                //         },
                //     },
                //     },
                // ],
                // color: ["#2ec7c9", "#b6a2de"],
                // series: [
                //     {
                //         name:'新增用户',
                //         data:data.userData.map(item=>item.new),
                //         type:'bar'
                //     },
                //     {
                //         name:'活跃用户',
                //         data:data.userData.map(item=>item.active),
                //         type:'bar'
                //     }
                // ],
                // }
                this.echartData.user.xData=data.userData.map(item=>item.date)
                this.echartData.user.series=[
                    {
                        name:'新增用户',
                        data:data.userData.map(item=>item.new),
                        type:'bar'
                    },
                    {
                        name:'活跃用户',
                        data:data.userData.map(item=>item.active),
                        type:'bar'
                    }
                ]
                // const U= echarts.init(this.$refs.userecharts)
                // U.setOption(userOption)
                //饼图
                // const videoOption={
                //     tooltip: {
                //         trigger: "item",
                //     },
                //     color: [
                //         "#0f78f4",
                //         "#dd536b",
                //         "#9462e5",
                //         "#a6a6a6",
                //         "#e1bb22",
                //         "#39c362",
                //         "#3ed1cf",
                //     ],
                //     series: [
                //         {
                //             data:data.videoData,
                //             type:'pie'
                //         }
                //     ],
                // }
                this.echartData.video.series=[
                        {
                            data:data.videoData,
                            type:'pie'
                        }
                    ]
                // const V= echarts.init(this.$refs.videoecharts)
                // V.setOption(videoOption)
            }
            console.log(res);
        })
    }
}
</script>
<style lang="less" scoped>
.user {
    padding-bottom: 20px;
    margin-bottom: 20px;
    border-bottom: 1px solid #ccc;
    display: flex;
    align-items: center;
    img {
        margin-right: 40px;
        width: 150px;
        height: 150px;
        border-radius: 50%;
    }
    .userinfo {
        .name {
            font-size: 32px;
            margin-bottom: 10px;
        }
        .access {
            color: #999999;
        }
    }
}
.login-info {
    p {
        line-height: 28px;
        font-size: 14px;
        color: #999999;
        span {
            color: #666666;
            margin-left: 60px;
        }
    }
}
.num {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    .icon {
        width: 90px;
        height: 90px;
        font-size: 30px;
        text-align: center;
        line-height: 90px;
        color: #fff;
    }
    .detail {
        margin-left: 15px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        .price {
            font-size: 30px;
            margin-bottom: 10px;
            line-height: 5px;
            height: 5px;
        }
        .desc {
            font-size: 14px;
            color: #999;
            text-align: center;
        }
    }
    .el-card {
        width: 32%;
        margin-bottom: 20px;
    }
}
.graph {
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
    .el-card {
        width: 48%;
    }
}
</style>