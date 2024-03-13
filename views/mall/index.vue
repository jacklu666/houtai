<template>
    <el-row :gutter="20">
        <el-col :span="8" style="margin-top: 20px;">
            <el-card shadow="hover">
                <div class="mstop">
                    <img src="../../src/assets/images/user.png" alt="">
                    <div class="mstop-right">
                        <p class="username">Admin</p>
                        <p class="name">超级管理员</p>
                    </div>
                </div>
                <div class="msfot">
                    <p>上次登录时间<span>2023-4-9</span></p>
                    <p>上次登录地点<span>东莞</span></p>
                </div>
            </el-card>
            <el-card shadow="hover" style="margin-top: 20px;height: 460px;">
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="date" label="课程" width="100">
                    </el-table-column>
                    <el-table-column prop="name" label="今日购买" width="100">
                    </el-table-column>
                    <el-table-column prop="address" label="本月购买" width="100">
                    </el-table-column>
                    <el-table-column prop="phone" label="总购买" width="100">
                    </el-table-column>
                </el-table>
            </el-card>
        </el-col>
        <el-col style="margin-top: 20px;" :span="16">
            <div class="num">
                <el-card shadow="hover" v-for="item in countData" :key="item.name" :body-style="{ display: 'flex', padding: 0 }">
                    <i class="icon" :class="`el-icon-${item.icon}`" :style="{ background: item.color }"></i>
                    <div class="num-detail">
                        <p class="price">￥{{ item.value }}</p>
                        <p class="iname">{{ item.name }}</p>
                    </div>
                </el-card>
            </div>
            <el-card style="height: 280px;">
                <Echarts :chartData="echartData.order" style="height: 260px;"></Echarts>
            </el-card>
            <div class="uservideo">
                <el-card style="height: 280px;">
                    <Echarts :chartData="echartData.user" style="height: 240px;"></Echarts>
                </el-card>
                <el-card style="height: 280px;">
                    <Echarts :chartData="echartData.video" :isAxisChart="false" style="height: 240px;"></Echarts>
                </el-card>
            </div>
        </el-col>
    </el-row>
</template>
<script>
import Echarts from '@/components/Echarts.vue';
import {getData} from '../../api/data'
export default {
    name: 'Mall',
    data() {
        return {
            tableData: [{
                date: 'oppo',
                name: 500,
                address: 3500,
                phone: 22000
            }, {
                date: 'oppo',
                name: 500,
                address: 3500,
                phone: 22000
            }, {
                date: 'oppo',
                name: 500,
                address: 3500,
                phone: 22000
            }, {
                date: 'oppo',
                name: 500,
                address: 3500,
                phone: 22000
            }],
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
            echartData: {
                order: {
                    xData: [],
                    series: []
                },
                user: {
                    xData: [],
                    series: []
                },
                video: {
                    series: []
                }
            }
        }
    },
    mounted(){
        getData().then(res=>{
            const {code,data}=res.data
            if(code===20000){
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
                this.echartData.order.xData=xData
                this.echartData.order.series=series
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
                this.echartData.video.series=[
                        {
                            data:data.videoData,
                            type:'pie'
                        }
                    ]
            }
            console.log(res);
        })
    },
    components: {
        Echarts
    }
}
</script>

<style lang="less" scoped>
.mstop {
    width: 100%;
    display: flex;
    align-items: center;
    padding-bottom: 10px;
    margin-bottom: 10px;
    border-bottom: 1px solid #333;

    img {
        width: 150px;
        height: 150px;
        border-radius: 50%;
        margin-right: 40px;
    }

    .mstop-right {

        .username {
            font-size: 32px;
            margin-bottom: 10px;
        }

        .name {
            color: #999;
        }
    }
}

.msfot {

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
        line-height: 90px;
        font-size: 30px;
        color: white;
        text-align: center;
    }

    .el-card {
        width: 32%;
        height: 90px;
        margin-bottom: 20px;
    }

    .num-detail {
        display: flex;
        flex-direction: column;
        justify-content: center;
        margin-left: 20px;

        .price {
            font-size: 30px;
            margin-bottom: 10px;
            line-height: 5px;
            height: 5px;
        }

        .iname {
            font-size: 14px;
            color: #999;
            text-align: center;
        }
    }
}
.uservideo{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 20px;
    .el-card{
        width: 48%;
    }
}
</style>