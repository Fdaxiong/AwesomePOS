<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-oeder" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tabeData" border style="width:100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-tab-pane>
          <div class="=totalDiv">
            <small>数量:</small>
            {{totaCount}} &nbsp;&nbsp;&nbsp;&nbsp;
            <small>金额:</small>
            {{totaMoney}}元
          </div>
          <div class="div-btn">
            <el-button type="warning">挂单</el-button>
            <el-button type="danger" @click="delAllgoods">删除</el-button>
            <el-button type="success" @click="checkout">结账</el-button>
          </div>
          <!-- 挂单页码 -->
          <el-tab-pane label="挂单"></el-tab-pane>
          <!-- 外卖页面 -->
          <el-tab-pane label="外卖"></el-tab-pane>
          <!-- 操作页码 -->
          <el-tab-pane label="操作"></el-tab-pane>
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
                  <li v-for=" hamburger in type0Goods" @click="addOrderList(hamburger)">
                    <!-- 图片用绑定属性的写法 -->
                    <span class="foodImg">
                      <img :src="hamburger.goodsImg" width="100%" />
                    </span>
                    <span class="foodName">{{hamburger.goodsName}}</span>
                    <span class="foodPrice">{{hamburger.price}}￥</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class="cookList">
                <li v-for=" hamburger in type1Goods" @click="addOrderList(hamburger)">
                  <!-- 图片用绑定属性的写法 -->
                  <span class="foodImg">
                    <img :src="hamburger.goodsImg" width="100%" />
                  </span>
                  <span class="foodName">{{hamburger.goodsName}}</span>
                  <span class="foodPrice">{{hamburger.price}}￥</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class="cookList">
                <li v-for=" hamburger in type2Goods" @click="addOrderList(hamburger)">
                  <!-- 图片用绑定属性的写法 -->
                  <span class="foodImg">
                    <img :src="hamburger.goodsImg" width="100%" />
                  </span>
                  <span class="foodName">{{hamburger.goodsName}}</span>
                  <span class="foodPrice">{{hamburger.price}}￥</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class="cookList">
                <li v-for=" hamburger in type3Goods" @click="addOrderList(hamburger)">
                  <!-- 图片用绑定属性的写法 -->
                  <span class="foodImg">
                    <img :src="hamburger.goodsImg" width="100%" />
                  </span>
                  <span class="foodName">{{hamburger.goodsName}}</span>
                  <span class="foodPrice">{{hamburger.price}}￥</span>
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
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tabeData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totaCount: 0,
      totaMoney: 0
    };
  },
  created: function() {
    // 后台传输数据常用商品
    axios
      .get("http://localhost:8080/static/oftenGoods.php")
      .then(reponse => {
        // console.log(reponse);
        this.oftenGoods = reponse.data.oftenGoods;
      })
      .catch(error => {
        // console.log(error);
        alert("网络错误,数据未加载");
      });

    // 加载商品分类数据
    axios
      .get("http://localhost:8080/static/type0Goods.php")
      .then(reponse => {
        console.log(reponse);
        this.type0Goods = reponse.data.type0Goods;
        this.type1Goods = reponse.data.type0Goods;
        this.type2Goods = reponse.data.type0Goods;
        this.type3Goods = reponse.data.type0Goods;
      })
      .catch(error => {
        // console.log(error);
        alert("网络错误,数据未加载");
      });
  },

  mounted: function() {
    var orderHeiht = document.body.clientHeight;
    // console.log(orderHeiht);
    document.getElementById("order-list").style.height = orderHeiht + "px";
  },
  methods: {
    addOrderList(goods) {
      this.totaMoney = 0;
      this.totaCount = 0;
      //商品是否存在订单中
      let isHave = false;
      for (let i = 0; i < this.tabeData.length; i++) {
        if (this.tabeData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据值判断接着写逻辑
      if (isHave) {
        //改变商品数量加1
        let arr = this.tabeData.filter(o => o.goodsId === goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };

        this.tabeData.push(newGoods);
      }
      //计算总价和数量
      this.tabeData.forEach(element => {
        this.totaCount += element.count;
        this.totaMoney = this.totaMoney + element.price * element.count;
      });
    },
    //删除单个商品
    delSingleGoods(goods) {
      this.tabeData = this.tabeData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    delAllgoods() {
      this.tabeData = [];
      this.totaCount = 0;
      this.totaMoney = 0;
    },
    //汇总数量和金额
    getAllMoney() {
      this.totaCount = 0;
      this.totaMoney = 0;
      if (this.tabeData) {
        //计算总价和数量
        this.tabeData.forEach(element => {
          this.totaCount += element.count;
          this.totaMoney = this.totaMoney + element.price * element.count;
        });
      }
    },
    //模拟结账
    checkout() {
      if (this.totaCount != 0) {
        this.tabeData = [];
        this.totaCount = 0;
        this.totaMoney = 0;

        this.$message({
          message: "假装 付钱成功,十分感谢",
          type: "success"
        });
      } else {
        this.$message.error("不能空结账,请点餐");
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-oeder {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
  height: 100%;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
  cursor: pointer;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
}
.cookList li {
  list-style: none;
  /* width:23%;
       height:auot; */
  width: 30%;
  height: auto;
  border: 1px solid #e5e9f2;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10%;
  padding-top: 10px;
}
.totalDiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #33dd33;
}
</style>
