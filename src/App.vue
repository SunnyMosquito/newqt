<template>
  <div id="app">
    <table>
      <caption>收款项目明细</caption>
      <tbody>
        <tr>
          <th>收费项目</th>
          <th>备注说明</th>
          <th>单价</th>
          <th>单位</th>
          <th>数量</th>
          <th>金额</th>
          <th>每年递增</th>
        </tr>
        <tr v-for="(item,index) in charge" :key="index">
          <td>{{item.name}}</td>
          <td>
            <input type="text">
          </td>
          <td>
            <input type="text" v-model="item.price">
          </td>
          <td>{{item.unit}}</td>
          <td>
            <input type="text" v-model="item.num">
          </td>
          <td>
            <input type="text" :value="toDecimal2(item.price * item.num)" disabled>
          </td>
          <td>
            <select v-model="item.incr">
              <option v-for="(i,index) in 10" :key="index" :value="i">{{i}}</option>
            </select>
            <input
              type="text"
              :value="incr(item.incrunit, item.incr)"
              @change="incrChange(index, $event)"
            >
            <span class="close" @click="removeCharge(index)"></span>
          </td>
        </tr>
      </tbody>
    </table>
    <div>
      <input type="button" value="添加收款明细项" @click="addCharge">
    </div>
  </div>
</template>

<script>
const JSONDATA =
  '{"rows": [{ "id": 4047, "name": "物业费", "unit": "元/月·平方", "price": 5.0, "incr": 0.0, "incrunit": 1, "remark": "", "money": 790.0, "num": 158.0 },{ "id": 4048, "name": "租金", "unit": "元/月·平方", "price": 28.0, "incr": 1.0, "incrunit": 2, "remark": "", "money": 4424.0, "num": 158.0 },{ "id": 4049, "name": "分摊水电费", "unit": "元/月·平方", "price": 0.3, "incr": 0.0, "incrunit": 1, "remark": "", "money": 47.4, "num": 158.0}]}';

export default {
  name: "app",
  data() {
    return {
      selected: null,
      charge: null
    };
  },
  mounted() {
    this.charge = JSON.parse(JSONDATA).rows;
    this.charge.forEach(element => {
      element.price = this.toDecimal2(element.price);
      element.money = this.toDecimal2(element.money);
    });
  },
  methods: {
    toDecimal2(x) {
      if (!parseFloat(x)) {
        return "0.00";
      }
      let f = Math.round(x * 100) / 100;
      let s = f.toString();
      let rs = s.indexOf(".");
      if (rs < 0) {
        rs = s.length;
        s += ".";
      }
      while (s.length <= rs + 2) {
        s += "0";
      }
      return s;
    },
    addCharge() {
      let name = prompt("请输入收费项目");
      let item = {
        name: name,
        unit: "元/月·平方",
        price: 0,
        incr: 0,
        num: 0,
        incrunit: 1,
        remark: "",
        money: 0
      };
      this.charge.push(item);
    },
    removeCharge(index) {
      if (confirm("你确定要删除吗？")) {
        this.charge.splice(index, 1);
      }
    },
    incr(incrunit, incr) {
      if (incrunit === 1) {
        return incr + "%";
      } else if (incrunit === 2) {
        return incr + "元";
      } else {
        return incr;
      }
    },
    incrChange(index, event) {
      this.charge[index].incr = parseInt(event.target.value);
      this.charge.splice(index, 1, this.charge[index]);
    }
  }
};
</script>

<style lang="scss">
body {
  background-color: #e1f0ff;
}
#app {
  color: #2c3e50;
  margin-top: 60px;
}
table {
  background-color: #ffffff;
  border-collapse: collapse;
  font-size: 14px;
  caption {
    text-align: left;
    color: #6360ff;
  }
  tr {
    height: 30px;
    &:first-child {
      background-color: #cde3fc;
      height: 24px;
    }
    th {
      border: 1px solid gray;
    }
    td {
      border: 1px solid gray;
      padding: 2px 4px;
      input[type="text"] {
        height: 24px;
        border: 1px solid #e7e7e7;
        border-radius: 4px;
        text-align: right;
        padding: 1px 2px;
        &:hover,
        &:focus {
          outline: none;
        }
      }
      &:nth-child(1) {
        width: 200px;
      }
      &:nth-child(2) {
        input[type="text"] {
          width: 80px;
        }
      }
      &:nth-child(3),
      &:nth-child(5) {
        input[type="text"] {
          width: 60px;
          border-left: 3px solid #d73e3e;
        }
      }
      &:nth-child(4) {
        width: 100px;
      }
      &:nth-child(6) {
        input[type="text"] {
          width: 90px;
          border-left: 3px solid #d73e3e;
          &:disabled {
            background-color: white;
          }
        }
      }
      &:nth-child(7) {
        position: relative;
        input[type="text"] {
          width: 80px;
          position: absolute;
          border-left: 3px solid #d73e3e;
          border-top-right-radius: 0;
          border-bottom-right-radius: 0;
          border-right: none;
          left: 4px;
        }
        select {
          width: 100px;
          height: 28px;
          border: 1px solid #e7e7e7;
          background: #ffffff;
          border-radius: 4px;
          &:hover,
          &:focus {
            outline: none;
          }
        }
        span.close {
          position: absolute;
          right: -38px;
          cursor: pointer;
          color: #6360ff;
          font-size: 14px;
          top: 0;
          width: 33px;
          height: 33px;
          line-height: 33px;
          &::after {
            content: "\2716";
          }
        }
      }
    }
  }
}
input[type="button"] {
  padding: 3px 8px;
  background-color: #ffffff;
  border: 1px solid #d8d8de;
  border-radius: 2px;
  margin-top: 20px;
  font-size: 12px;
  color: #6360ff;
}
</style>
