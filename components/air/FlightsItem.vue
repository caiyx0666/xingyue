<template>
    <div class="flight-item">
        <div>
            <!-- 显示的机票信息 -->
            <el-row type="flex" align="middle" class="flight-info">
                <el-col :span="6"> <span>{{data.airline_name}} </span> {{data.flight_no}} </el-col>
                <el-col :span="12">
                    <el-row
                        type="flex"
                        justify="space-between"
                        class="flight-info-center"
                    >
                        <el-col :span="8" class="flight-airport">
                            <strong>{{data.dep_time}}</strong>
                            <span>{{data.org_airport_name}}{{data.org_airport_quay}}</span>
                        </el-col>
                        <!-- 飞行时间在数据中没有需要自己计算 -->
                        <el-col :span="8" class="flight-time">
                            <span>{{duration}}</span>
                        </el-col>
                        <el-col :span="8" class="flight-airport">
                            <strong>{{data.arr_time}}</strong>
                            <span>{{data.dst_airport_name}}{{data.dst_airport_quay}}</span>
                        </el-col>
                    </el-row>
                </el-col>
                <el-col :span="6" class="flight-info-right">
                    ￥<span class="sell-price">{{bestPrice}}</span>起
                </el-col>
            </el-row>
        </div>
        <el-collapse-transition>
            <div class="flight-recommend" v-if="isShow">
                <!-- 隐藏的座位信息列表 -->
                <el-row type="flex" justify="space-between" align="middle">
                    <el-col :span="4">低价推荐</el-col>
                    <el-col :span="20">
                        <el-row
                            type="flex"
                            justify="space-between"
                            align="middle"
                            class="flight-sell"
                            v-for="(seat, index) in data.seat_infos"
                            :key="index"
                        >
                            <el-col :span="16" class="flight-sell-left">
                                <span>{{seat.group_name}}</span> | {{seat.supplierName}}
                            </el-col>
                            <el-col :span="5" class="price"> ￥{{seat.settle_price}} </el-col>
                            <el-col :span="3" class="choose-button">
                                <el-button type="warning" size="mini" @click.stop="handleSubmit(seat.seat_xid)">
                                    选定
                                </el-button>
                                <p v-if="seat.nums != 'A'">剩余：{{seat.nums}}</p>
                            </el-col>
                        </el-row>
                    </el-col>
                </el-row>
            </div>
        </el-collapse-transition>
    </div>
</template>

<script>
    export default {
        props: {
            data: {
                type: Object,
                default() {
                    return {}
                }
            },
            isShow: Boolean
        },
        computed: {
            duration() {
                // 先将触发和到达时间都变成分钟
                const depTimeNum = Number(this.data.dep_time.split(":")[0] * 60) + Number(this.data.dep_time.split(":")[1])
                const arrTimeNum = Number(this.data.arr_time.split(":")[0] * 60) + Number(this.data.arr_time.split(":")[1])
                let durationNum = arrTimeNum - depTimeNum

                if (durationNum < 0) {
                    durationNum = durationNum + (24 * 60)
                }
                // 相减之后换回小时:分钟的格式
                return Math.floor(durationNum/60) + '时' + durationNum%60 + "分"
            },
            bestPrice() {
                // 遍历当前航班座位信息
                // 找出最低价 return 出来
                let bestPrice = this.data.seat_infos[0].settle_price_child
                this.data.seat_infos.forEach(seat => {
                    // 当前座位价格  是 seat.settle_price_child
                    if (seat.settle_price_child < bestPrice) {
                        bestPrice = seat.settle_price_child
                    }
                });
                return bestPrice
            }
        },
        methods: {
            handleSubmit(seat_xid) {
                console.log('hahaha');
                this.$router.push({
                    path: '/air/order',
                    query: {
                        id: this.data.id,
                        seat_xid
                    }
                })
            }
        }
    };
</script>

<style scoped lang="less">
    .flight-item{
        border:1px #ddd solid;
        margin-bottom: 10px;

        .flight-info{
            padding:15px;
            cursor: pointer;

            > div{
                &:first-child, &:last-child{
                    text-align: center;
                }
            }
        }

        .flight-info-center{
            padding:0 30px;
            text-align: center;

            .flight-airport{
                strong{
                    display: block;
                    font-size:24px;
                    font-weight: normal;
                }
                span{
                    font-size: 12px;
                    color:#999;
                }
            }

            .flight-time{
                span{
                    display: inline-block;
                    padding:10px 0;
                    border-bottom: 1px #eee solid;
                    color:#999;
                }
            }
        }

        .flight-info-right{
            
            .sell-price{
                font-size: 24px;
                color:orange;
                margin:0 2px;
            }
        }
    }

    .flight-recommend{
        background: #f6f6f6;
        border-top:1px #eee solid;
        padding:0 20px;

        .flight-sell{
            border-bottom:1px #eee solid;
            padding:10px 0;

            &:last-child{
                border-bottom: none;
            }

            .flight-sell-left{
                font-size: 12px;
                span{
                    color:green;
                }
            } 

            .price{
                font-size: 20px;
                color:orange;
            }

            .choose-button{
                text-align: center;
                color:#666;
                button{
                    display: block;
                    width:100%;
                    margin-bottom:5px;
                }
            }
        }
    }
</style>