<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label='点餐'>
            <el-table :data='tableData' border style='with:100%'>
              <el-table-column prop='goodsName' label='商品名称'></el-table-column>
              <el-table-column prop='count' label='数量' width='50'></el-table-column>
              <el-table-column prop='price' label='金额' width='70'></el-table-column>
              <el-table-column label='操作' width='100' fixed='right'>
                <template slot-scope='scope'>
                  <el-button type='text' size='small'>删除</el-button>
                  <el-button type='text' size='small' @click='addOrderList(scope.row)'>增加</el-button>
                </template>
              </el-table-column>
              
            </el-table>
            <div class='totaDiv'>
            <small>数量:</small> {{totaCount}} &nbsp;&nbsp;<small>金额:</small> {{totaMoney}}元
            </div>
            <div class='div-btn'>
              <el-button type='warning'>挂单</el-button>
              <el-button type='danger'>删除</el-button>
              <el-button type='success'>结账</el-button>
            </div>


          </el-tab-pane>        
          <el-tab-pane label='挂单'>
            挂单
          </el-tab-pane> 
          <el-tab-pane label='外卖'>
            外卖
          </el-tab-pane> 
        </el-tabs>

      </el-col>
      <el-col :span="17">
        <div class='often-goods'>
          <div class='title'>常用商品</div>
          <div class='often-goods-list'>
            <ul>
              <li v-for='goods in oftenGoods' @click='addOrderList(goods)'>
                <span>{{goods.goodsName}}</span>
                <span class='o-price'>￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>

          <div class='goods-type'>
            <el-tabs>
              <el-tab-pane label='汉堡'>
                
                <div> 
                  <ul class='cookList'>
                      <li v-for='goods in type0Goods' @click='addOrderList(goods)'>
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                  </ul>
                </div>

              </el-tab-pane>
              <el-tab-pane label='小食'>
                <div> 
                  <ul class='cookList'>
                      <li v-for='goods in type1Goods' @click='addOrderList(goods)'>
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label='饮料'>
                <div> 
                  <ul class='cookList'>
                      <li v-for='goods in type2Goods' @click='addOrderList(goods)'>
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                          <span class="foodName">{{goods.goodsName}}</span>
                          <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label='套餐'>
                <div> 
                  <ul class='cookList'>
                      <li v-for='goods in type3Goods' @click='addOrderList(goods)'>
                          <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
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
import axios from 'axios';
export default {
  name: 'pos',
  data(){
    return{
         tableData:[],
         oftenGoods:[], 
         type0Goods:[],
         type1Goods:[],
         type2Goods:[],
         type3Goods:[],
         totaMoney:0,
         totaCount:0,
    }
  },
  created:function(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(response=>{
      console.log(response),
      this.oftenGoods=response.data;
    })
    .catch(error=>{
      // console.log(error)
      alert('网络错误,不能查询')
    })

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(response=>{
      // console.log(reponse),
      this.type0Goods=response.data[0];
      this.type1Goods=response.data[1];
      this.type2Goods=response.data[2];
      this.type3Goods=response.data[3];
    })
    .catch(error=>{
      // console.log(error)
      alert('网络错误,不能查询')
    })
  },
  
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    
    document.getElementById('order-list').style.height=orderHeight+'px';
  },
  methods:{
    addOrderList(goods){
      this.totaMoney=0;
      this.totaCount=0;
      // 商品是否已存在于订单列表中
      let isHave=false;
      for(let i=0;i<this.tableData.length;i++){
        if(this.tableData[i].goodsId==goods.goodsId){
          isHave=true;
        }
      }
      // 根据判断的值编写业务逻辑
      if(isHave){
        let arr = this.tableData.filter(o=>o.goodsId == goods.goodsId);
        arr[0].count++;
      }else{
        let newGoods = {goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1}
        this.tableData.push(newGoods);
      }

// 计算汇总金额和数量
      this.tableData.forEach((element)=>{
        this.totaCount+=element.count;
        this.totaMoney=this.totaMoney+(element.price*element.count)
      })
    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.totaDiv{
  background-color:#fff;
  padding:10px;
  border-bottom:1px solid #d3dce6;
}
.pos-order{
  background-color:#f9fafc;
  border-right:1px solid #c0ccda;
}
.div-btn{
  margin-top:10px;
}
.title{
  height:20px;
  border-bottom:1px solid #03dce6;
  background-color:#f9fafc;
  padding:10px;
  text-align:left;
}
.often-goods-list ul li{
  list-style:none;
  float:left;
  border:1px solid #e5e9f2;
  padding:10px;
  margin:10px;
  background-color:#fff;
  border-radius:5px;
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
</style>
