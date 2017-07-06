<template>
    <div class='pos'>
        <el-row>
            <el-col :span='7' class='pos-order' id='order-list'>
                <el-tabs>
                    <el-tab-pane label="点餐">
                        <el-table :data="tableData" border style="width:100%;">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" label="数量" width="70"></el-table-column>
                            <el-table-column prop="price" label="金额" width="70"></el-table-column>
                            <el-table-column label="操作" width="100" fixed="right">
                                <template scope="scope">
                                    <el-button type="text" size="small">删除</el-button>
                                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="total-div">
                            <small>共计：{{totalNum}}</small>
                            <small>金额：{{totalMoney}}元</small>
                        </div>
                        <div class="div-btn">
                            <el-button type="warning">挂单</el-button>
                            <el-button type="danger">删除</el-button>
                            <el-button type="success">结账</el-button>
                        </div>
                    </el-tab-pane>
                    <el-tab-pane label="挂单">
                        挂单
                    </el-tab-pane>
                    <el-tab-pane label="外卖">
                        外卖
                    </el-tab-pane>
                </el-tabs>
            </el-col>    
            <el-col :span="17">
                <div class="often-goods">
                    <div class="title">常用商品</div>
                    <div class="often-goods-list">
                        <ul>
                            <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                                <span>{{goods.goodsName}}</span>
                                <span class="o-price">￥{{goods.price}}元</span>
                            </li>
                        </ul>
                    </div>
                </div>

                <div class="goods-type">
                    <el-tabs>
                        <el-tab-pane label="汉堡">
                            <div>
                                <ul class="cookList">
                                    <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="小食">
                            <ul class="cookList">
                                <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                    <span class="foodName">{{goods.goodsName}}</span>
                                    <span class="foodPrice">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <ul class="cookList">
                                <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                    <span class="foodName">{{goods.goodsName}}</span>
                                    <span class="foodPrice">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                            <ul class="cookList">
                                <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                    <span class="foodName">{{goods.goodsName}}</span>
                                    <span class="foodPrice">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                        </el-tab-pane>
                    </el-tabs>
                </div>
            </el-col>    
        </el-row>
        
    </div>    
</template>

<script>
import axios from 'axios';
export default {
    name:'Pos',
    data(){
        return {
            tableData: [],
            oftenGoods:[],
            type0Goods:[],
            type1Goods:[],
            type2Goods:[],
            type3Goods:[],
            totalNum:0,
            totalMoney:0
        }
    },
    created:function(){
        axios.get("http://jspang.com/DemoApi/oftenGoods.php")
        .then(response=>{
            this.oftenGoods=response.data;
        })
        .catch(error=>{
           alert("网路错误，不能访问");
        });
        axios.get('http://jspang.com/DemoApi/typeGoods.php')
        .then(response=>{
            this.type0Goods=response.data[0];
            this.type1Goods=response.data[1];
            this.type2Goods=response.data[2];
            this.type3Goods=response.data[3];
        })
        .catch(error=>{
            alert("网络错误，不能访问");
        })
    },
    mounted:function(){
        var orderHeight=document.body.clientHeight;
        var orderList=document.getElementById('order-list');
        orderList.style.height=orderHeight+'px';
    },
    methods:{
        addOrderList:function(goods){  
            this.totalNum=0;
            this.totalMoney=0;
            var  isHave=false;
            //判断tableData中有没有商品
            for(var i=0;i<this.tableData.length;i++){
                if(this.tableData[i].goodsId==goods.goodsId){
                    isHave=true;
                }
            }
            //如果有的话数量+1，如果没有就把新商品添加到tableData中
            if(isHave){
                var arr=this.tableData.filter(o=>
                    o.goodsId==goods.goodsId
                )
                arr[0].count++;
            }else{
                var newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
                this.tableData.push(newGoods);
            }

            for(var i=0;i<this.tableData.length;i++){
                this.totalNum+=this.tableData[i].count;
                this.totalMoney=this.totalMoney+(this.tableData[i].count*this.tableData[i].price)
            }
        }
    }
    
}
</script>

<style scorped>
.pos-order{
    background-color:#f9fafc;
    border-right:1px solid #c0ccda;
}
.div-btn{
    margin-top:10px;
}
.title{
    height:20px;
    border-bottom:1px solid #d3dced;
    background:#f9fafc;
    padding:10px;
    text-align:left;
}
.often-goods-list ul li{
    list-style:none;
    float:left;
    border:1px solid #e5e9e2;
    padding:10px;
    margin:10px;
    background-color:#fff;
    cursor:pointer;
}
.o-price{
    color:#58b7ff;
}
.goods-type{
    clear:both;
}
.cookList li{
    list-style: none;
    width:23%;
    border:1px solid #E5E9F2;
    height: auot;
    overflow: hidden;
    background-color:#fff;
    padding: 2px;
    float:left;
    margin: 2px;
    cursor:pointer;
   }
.cookList li span{
    display: block;
    float:left;
}
.foodImg{
    width: 40%;
    margin-top:1px;
}
.foodName{
    font-size: 18px;
    padding-left: 10px;
    color:brown;
}
.foodPrice{
    font-size: 16px;
    padding-left: 10px;
    padding-top:10px;
}
.total-div{
    padding:10px 0;
    border-bottom:1px solid #dfe6ec;
}
.total-div small{
    width:30%;
    display:inline-block;
}
</style>
