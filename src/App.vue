<script setup>
import axios from "axios"
import { ref } from "vue";

const stockData = ref([])  // for caching response object
const stockSymbolStr = ref()  // input 
const stocks = ref()
const stock = ref()
const stockSymbolArray = ref([])

const addStockDialogVisible = ref(false)

const tableHeaderArray = ref(["股價", "漲跌", "漲跌幅(%)", "委買價", "委賣價", "開盤", "昨收", "最高", "最低", "成交量(張)", "更新時間"])

const submitstockSymbolArray = async () => {

  const res = await Promise.all(
    stockSymbolArray.value.map((stock) => axios.get(`http://127.0.0.1:3000/stockData/${stock}`))
  )

  const resData = res.map((element) => element.data)
  stockData.value = resData
}

const addStockTemp = () => {
  stockSymbolArray.value.push(stock.value)
}
</script>

<template>

  <header>
    <div class="title">
      <img src="../public/favicon.ico">
      <h1>小軟理財網</h1>
    </div>
  </header>

  <main class="opacity">
    <div class="stockPicking w1260">
      <div class="table">
        <button @click="addStockDialogVisible = true">新增選股</button>

        <div class="tableHeader">

          <div class="tableSymbol">
            <div class="checkBox">
              <span class="checkBox"><svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                  width="20" height="20" viewBox="0 0 24 24">
                  <defs>
                    <path id="prefix__checkboxunselected-a"
                      d="M2,2 L20,2 L20,20 L2,20 L2,2 Z M21,0 L1,0 C0.447,0 0,0.448 0,1 L0,21 C0,21.552 0.447,22 1,22 L21,22 C21.552,22 21.999,21.552 21.999,21 L21.999,1 C21.999,0.448 21.552,0 21,0 L21,0 Z">
                    </path>
                  </defs>
                  <use fill="#979ba7" fill-rule="evenodd" transform="translate(1 1)"
                    xlink:href="#prefix__checkboxunselected-a"></use>
                </svg></span>
            </div>
            <p>股名/股號</p>
          </div>
          <div class="tableCell" v-for="item in tableHeaderArray">
            {{ item }}
          </div>
        </div>
        <ul class="tableBody">
          <li class="tableRow" v-for="item in stockData">
            <div class="tableSymbol">
              <div class="checkBox">
                <span class="checkBox">口</span>
              </div>
              <p>{{ item.stockName }}</p>
            </div>
            <div class="tableCell" v-for="col in item.info">
              {{ col }}
            </div>
          </li>
        </ul>
      </div>

    </div>
  </main>

  <div class="dialogContainer" v-if="addStockDialogVisible">
    <div class="overlay"></div>
    <div class="addStockDialog">
      <div>
        <input type="text" placeholder="請輸入股票代碼" v-model="stock">
        <button @click="addStockTemp()">新增股票</button>
        <ul>
          <li v-for="stock in stockSymbolArray">{{ stock }}</li>
        </ul>
        <button @click="addStockDialogVisible = false, submitstockSymbolArray()" >確認</button>
      </div>
    </div>
  </div>

</template>

<style>
* {
  margin: 0;
  padding: 0;
}

.overlay {
  z-index: 0;
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background-color: rgba(0, 0, 0, .5);
  overflow-y: auto;
}

.addStockDialog {
  z-index: 1;
  position: absolute;
  background-color: blanchedalmond;
  width: 500px;
  min-height: 400px;
  top: 150px;
  left: 50%;
  transform: translate(-50%, 0);
  border-radius: 20px;
}


header {
  background-color: gray;
  margin-bottom: 20px;
}

.title {
  padding: 20px;
  height: auto;
  display: flex;
  align-content: center;
  flex-direction: row;
  align-items: center;
}

.stockPicking {
  height: 100%;
  margin-bottom: 32px;
  margin-left: auto;
  margin-right: auto;
}


.w1260 {
  width: 1260px;
}

.table {
  margin-bottom: 30px;
  text-align: center;
}

.tableHeader {
  height: 50px;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  background-color: aqua;
}

.tableRow {
  background-color: beige;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}

.tableBody :hover {
  background-color: bisque;
}


.tableSymbol {
  width: 150px;
  display: flex;
  flex-direction: row;
  padding: 10px;
}

div .checkBox {
  margin-right: 5px;
}

.tableCell {
  width: 8.3%;
  text-align: center;
  padding: 10px;
}
</style>
