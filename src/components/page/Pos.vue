<template>
  <div class="pos">
      <el-row>
          <el-col :span="7" class="pos-order" id="order-list">
              <el-tabs>
                  <el-tab-pane label="点餐">
                      <el-table border :data="tabelData">
                          <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                          <el-table-column prop="count" label="数量"></el-table-column>
                          <el-table-column prop="price" label="金额"></el-table-column>
                          <el-table-column  label="操作" fixed="right">
                              <template scope="scope">
                                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                                  <el-button type="text" size="small" @click="deleteSingleGoods(scope.row)">删除</el-button>
                              </template>
                          </el-table-column>
                      </el-table>
                      <div class="computedMonery">
                        <span>数量：{{totalCount}}</span>
                        <span>金额：{{totalMoney}}</span>
                      </div>
                      <div class="paymenu">
                        <el-button type="warning" >挂单</el-button>
                        <el-button type="warning" @click="deleteAllgoods">删除</el-button>
                        <el-button type="success" @click="checkout">结账</el-button>
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
                    <li v-for="(goods,index) in oftenGoods" :key = index @click="addOrderList(goods)">
                      <span>{{goods.goodsName}}</span>
                      <span class="o-price">¥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </div>
              <div class="goods-type">
                  <el-tabs>
                    <el-tab-pane label="汉堡">
                        <div>
                          <ul class='cookList'>
                            <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                              <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                              <span class="foodName">{{goods.goodsName}}</span>
                              <span class="foodPrice">￥{{goods.price}}元</span>
                            </li>
                          </ul>
                        </div>
                    </el-tab-pane>
                    <el-tab-pane label="小食">
                      <div>
                        <ul class='cookList'>
                          <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                            <span class="foodImg"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                      </div>
                    </el-tab-pane>
                    <el-tab-pane label="饮料">
                      <div>
                        <ul class='cookList'>
                          <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                            <span class="foodImg"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                      </div>
                    </el-tab-pane>
                    <el-tab-pane label="套餐">
                      <div>
                        <ul class='cookList'>
                          <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                            <span class="foodImg"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                          </li>
                        </ul>
                      </div>
                    </el-tab-pane>
                  </el-tabs>
              </div>


          </el-col>
      </el-row>
  </div>
</template>
<script>
  // import axios from 'axios'
  export default {
    name: 'pos',
    data(){
        return {
          tabelData:[],
          oftenGoods: [],
          type0Goods:[],
          type1Goods:[],
          type2Goods:[],
          type3Goods:[],
          totalMoney: 0,
          totalCount: 0
        }
    },
    created(){
      //读取常用商品列表
      this.axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods\n' +
        '\n')
        .then(response=>{
          console.log(response);
          this.oftenGoods=response.data;
        })
        .catch(error=>{
          console.log(error);
          alert('网络错误，不能访问');
        })
      //读取分类商品列表
        this.axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
          .then(response=>{
            console.log(response);
            //this.oftenGoods=response.data;
            this.type0Goods=response.data[0];
            this.type1Goods=response.data[1];
            this.type2Goods=response.data[2];
            this.type3Goods=response.data[3];
          })
          .catch(error=>{
            console.log(error);
            alert('网络错误，不能访问');
          })
    },
    mounted() {
        var orderHeight = document.body.clientHeight;
        document.getElementById('order-list').style.height = orderHeight + 'px';
    },
   methods: {
      addOrderList(goods){
        //先判断商品是否存在列表中
        console.log(goods)
        let isHave = false;
        for(let i=0; i<this.tabelData.length;i++){
            if(this.tabelData[i].goodsId == goods.goodsId){
              isHave = true;
            }
        }
        // 根据isHave的值判断商品是否一经存在列表中
        if(isHave) {
          // 如果存在商品数量加一
          let arr = this.tabelData.filter(a => a.goodsId == goods.goodsId);
          arr[0].count++;
        } else {
          //如果不存在构造新数组 推进tabelData
          let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
          this.tabelData.push(newGoods);
        }
        this.calPrice()
      },
     //删除单个商品
      deleteSingleGoods(goods){
        this.tabelData = this.tabelData.filter(a => a.goodsId != goods.goodsId)
        this.calPrice()
      },
     // 删除所有商品
     deleteAllgoods(){
       this.tabelData = [];
       this.totalCount = 0;
       this.totalMoney = 0;
     },
     //模拟结账
     checkout(){
        if(this.totalCount != 0){

          this.tabelData = [];
          this.totalCount = 0;
          this.totalMoney = 0;
          this.$message({
            message: '结账成功，祝您用餐愉快',
            type: "success"
          })
        } else {
          this.$message({
            message: '结账失败，请先选择您需要的商品',
            type: "error"
          })
        }
     },
      //价格计算
     calPrice(){
        this.totalCount = 0;
        this.totalMoney = 0;
        if(this.tabelData.length > 0) {
          //计算汇总金额和数量
          this.tabelData.forEach((ele) => {
            this.totalMoney = this.totalMoney + (ele.count*ele.price);
            this.totalCount += ele.count;
          })
        }
     }
   }

  }
</script>
<style scoped lang="less">
  li {
    cursor: pointer;
  }
  .pos-order{
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
    .paymenu{
      margin: 40px 0;
    }
  }
  .often-goods {
    height: 20px;
    border-bottom: 1px solid #D3dce6;
    background-color: #F9FAFC;
    padding: 10px;
    text-align: left;
    .often-goods-list{
      ul li {
        list-style: none;
        float: left;
        border: 1px solid #D3dce6;
        padding: 10px;
        margin: 10px;
        background-color: white;
        .o-price {
          color: #0067ed;
        }
      }
    }
  }
  .goods-type{
    clear: both;
    .cookList li{
      list-style: none;
      width:23%;
      border:1px solid #E5E9F2;
      height: auto;
      overflow: hidden;
      background-color:#fff;
      padding: 2px;
      float:left;
      margin: 2px;
      cursor: pointer;
    }
    .cookList li span{
      display: block;
      float:left;
    }
    .foodImg{
      width: 40%;
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
  }
  .computedMonery{
    margin: 10px;
    text-align: center;
    span{
      padding: 0 20px;
    }
  }

</style>
