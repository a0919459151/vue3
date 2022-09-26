<script setup>
import axios from "axios"
import { ref } from "vue";

const stockData = ref([])  // for caching response object

// input 
const stock = ref('')
const stockSymbolArray = ref([])
const stockTemp = ref([])
const addStockDialogVisible = ref(false)
const tableHeaderArray = ref(["股價", "漲跌", "漲跌幅(%)", "委買價", "委賣價", "開盤", "昨收", "最高", "最低", "成交量(張)", "更新時間"])
const alertString = ref('')
const submitstockSymbolArray = async () => {
  if (stockTemp.value) {
    stockSymbolArray.value = stockSymbolArray.value.concat(stockTemp.value)
    stockTemp.value = []
  }

  const res = await Promise.all(
    stockSymbolArray.value.map((stock) => axios.get(`http://127.0.0.1:3000/stockData/${stock}`))
  )
  const resData = res.map((element) => element.data)
  stockData.value = resData
}

const addStockToTemp = () => {
  stock.value = stock.value.toUpperCase()
  if (stock.value) {
    if (stockIsExist()) {
      alertString.value = 'Stock is exist!'

    } else {
      stockTemp.value.push(stock.value)
    }
    stock.value = ""
  }

  function stockIsExist() {
    return stockSymbolArray.value.includes(stock.value) || stockTemp.value.includes(stock.value)
  }
}

const removeStockTemp = () => {
  stockTemp.value = []
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
      <button class="but" @click="addStockDialogVisible = true">新增選股</button>
      <button class="but" @click="submitstockSymbolArray()">刷新</button>

      <div class="table">

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
                </svg>
              </span>
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
                <span class="checkBox"><svg xmlns="http://www.w3.org/2000/svg"
                    xmlns:xlink="http://www.w3.org/1999/xlink" width="20" height="20" viewBox="0 0 24 24">
                    <defs>
                      <path id="prefix__checkboxunselected-a"
                        d="M2,2 L20,2 L20,20 L2,20 L2,2 Z M21,0 L1,0 C0.447,0 0,0.448 0,1 L0,21 C0,21.552 0.447,22 1,22 L21,22 C21.552,22 21.999,21.552 21.999,21 L21.999,1 C21.999,0.448 21.552,0 21,0 L21,0 Z">
                      </path>
                    </defs>
                    <use fill="#979ba7" fill-rule="evenodd" transform="translate(1 1)"
                      xlink:href="#prefix__checkboxunselected-a"></use>
                  </svg>
                </span>
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


  <Transition name="bounce">

    <div class="addStockDialogContainer" v-if="addStockDialogVisible">
      <div class="overlay" @click="addStockDialogVisible = false, removeStockTemp()"></div>

      <div class="addStockDialog">
        <h3>新增股票</h3>
        <div class="alertString" v-if="alertString">{{ alertString }}</div>
        <div class="inputWrapper">
          <div class="svg">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="#5F6368">
              <path
                d="M15.5 14h-.79l-.28-.27A6.471 6.471 0 0 0 16 9.5 6.5 6.5 0 1 0 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z" />
            </svg>
          </div>
          <input class="input" type="text" placeholder="請輸入股票代碼 <Enter>" v-model="stock" @keyup.enter="addStockToTemp()" @click="alertString = ''">
        </div>
        <ul style="width: 80%;">
          <li class="li" v-for="stock in stockTemp">
            <div>
              <p>{{ stock }}</p>
            </div>
            <div>
              <svg class="Cur(p)" width="22" height="22" viewBox="0 0 24 24" data-icon="star-filled"
                style="fill: rgb(250, 220, 0); stroke: rgb(0, 0, 0); stroke-width: 1; vertical-align: bottom;">
                <path
                  d="M22.954 8.952c-.126-.39-.49-.652-.9-.652H15.06L12.9 1.652C12.772 1.262 12.41 1 12 1s-.772.263-.898.652L8.94 8.3H1.945c-.41 0-.772.263-.898.652-.127.39.012.815.343 1.056l5.66 4.108-2.163 6.648c-.126.39.012.815.344 1.055.332.24.78.24 1.112 0L12 17.71l5.66 4.11c.165.12.36.18.555.18.194 0 .39-.06.555-.18.33-.24.47-.667.343-1.056l-2.16-6.648 5.658-4.108c.332-.24.47-.667.344-1.056z">
                </path>
              </svg>
            </div>
          </li>
        </ul>
        <button class="dialogSubmit" @click="addStockDialogVisible = false, submitstockSymbolArray()">送出</button>
      </div>
    </div>
  </Transition>


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
  display: flex;
  flex-direction: column;
  z-index: 1;
  position: absolute;
  background-color: blanchedalmond;
  width: 350px;
  min-height: 400px;
  top: 150px;
  left: 50%;
  transform: translate(-50%, 0);
  border-radius: 20px;
  padding: 20px 30px;
  align-items: center;
}

.bounce-enter-active {
  animation: bounce-in 0.5s;
}

.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }

  50% {
    transform: scale(1.25);
  }

  100% {
    transform: scale(1);
  }
}


.inputWrapper {
  margin-top: 5px;
  height: 40px;
  width: 97%;
  background-color: white;
  border: 1px solid #e0e4e9;
  border-radius: 8px;
  display: flex;
  align-items: center;
  padding: 5px;

}

.input {
  margin-left: 5px;
  width: 90%;
  height: 100%;
  border: none;
  font-size: 24px;
}

.input:focus {
  outline: none;
}

.alertString {
  color: red;
  font-weight: bold;
}

.li {
  font-size: 20px;
  list-style: none;
  margin: 5px;
  display: flex;
  justify-content: space-between;
}

.but {
  background-color: lightblue;
  margin: 10px;
  font-size: 14px;
  padding: 10px 28px;
  border-radius: 4px;
}

.but:hover {
  color: white;
  background-color: blueviolet;
}

.dialogSubmit {
  background-color: lightgreen;
  width: 200px;
  height: 50px;
  border-radius: 5px;
  position: absolute;
  bottom: 20px;
}

.dialogSubmit:hover {
  color: white;
  background-color: goldenrod;
}

header {
  width: 1440px;
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
  padding: 15px;
}

div .checkBox {
  margin-right: 5px;
}

.tableCell {
  width: 8.3%;
  text-align: center;
  padding: 15px 0px;
}
</style>
