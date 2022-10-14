<template>
  <div>
    <div class="data">
    <input
      v-model="search"
      placeholder="search"
      :style="{'margin-bottom':'10px', 'margin-right':'5px'}"
      @input="newData(search)">

      <select v-model="selected" @change="changeCurrency(selected)">
        <option v-for="opt in options" :key="opt.value">
          {{ opt.value }}
        </option>
      </select>

    <table class="table table-striped table-bordered">
    <thead>
      <tr>
        <th @click="sortByHouseFun">Placment
          <span v-if="sortByHouse == 1">↑</span>
          <span v-if="sortByHouse == 2">↓</span>
        </th>
        <th @click="sortByAnimalsFun">Animals 
          <span v-if="sortByAnimals == 1">↑</span>
          <span v-if="sortByAnimals == 2">↓</span>
        </th>
        <th @click="sortByPrizeFun()">Prize
          <span v-show="sortByPrize == 1">↑</span>
          <span v-show="sortByPrize == 2">↓</span>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in data" :key="item.animals">
        <td>{{ item.homePlacemant  }}</td>  
        <td>{{ item.animals }}</td> 
        <td>{{ Math.round(item.prize) }} {{ item.currency}}</td>  
      </tr>
    </tbody>
  </table>
  </div>
  <div class="form">
    <span id="form-title">Add new house</span>
      <div id="form-model">
        <input type="radio" id="ground" value="ground" v-model="kindOfHouse" />
        <label for="ground" :style="{'margin-right':'5px'}">Ground</label>

        <input type="radio" id="tree" value="tree" v-model="kindOfHouse" />
        <label for="tree">Tree</label>
        <br/>
        <input
        class="input"
        v-model="animal"
        placeholder="animal">
        <br/>
        <input
          class="input"
          type="number"
          v-model="prize"
          placeholder="prize">
        <br/>
        <button @click="addHouse">Add</button>


    </div>
  </div>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import { defineComponent, onMounted, ref } from 'vue';
import { IData } from '../types/data.type';
export default defineComponent({
  name: 'DataComponent',
  setup(){
    const data = ref<IData[] | undefined>(undefined);

    const allData = ref<IData[]>([
        {
          homePlacemant: 'ground',
          animals: 'dogs',
          prize: 200, 
          currency: 'PLN',
        },
        {
          homePlacemant: 'tree',
          animals: 'birds',
          prize: 50, 
          currency: 'PLN',
        },
        {
          homePlacemant: 'ground',
          animals: 'llamas',
          prize: 1500,
          currency: 'PLN',
        }
      ]);

    onMounted(() => {
      newData('')
    });

    const search = ref('');
    const kindOfHouse = ref('ground');
    const animal = ref('');
    const prize = ref(null)
    const sortByHouse = ref(0);
    const sortByAnimals = ref(0);
    const sortByPrize = ref(1);
    const baseCurrency = ref('PLN');
    const options = [{value: 'PLN'}, {value:'USD'}, {value: 'EUR'}, {value: 'GBP'}]
    const selected = ref(baseCurrency.value)


    function newData(search: string){
      data.value = allData.value.filter(obj => {
        return obj.homePlacemant.includes(search) || obj.animals.includes(search) || String(obj.prize).includes(search)
        }).sort((a, b) => { 
          return sortByHouse.value == 1 ? a.homePlacemant.toLowerCase() > b.homePlacemant.toLowerCase() ? 1 : -1 :
           sortByHouse.value == 2 ? a.homePlacemant.toLowerCase() < b.homePlacemant.toLowerCase() ? 1 : -1 : 
          sortByAnimals.value == 1 ? a.animals.toLowerCase() > b.animals.toLowerCase()? 1 : -1 : 
          sortByAnimals.value == 2 ? a.animals.toLowerCase() < b.animals.toLowerCase()? 1 : -1 : 
          sortByPrize.value == 1 ? a.prize > b.prize ? 1 : -1 : 
          sortByPrize.value == 2 ? a.prize < b.prize ? 1 : -1 : 0
      });
    }

    function clearForm(){
      animal.value = ''
      prize.value = null
    }

    function addHouse(){
      if(kindOfHouse.value && animal.value && prize.value){
        allData.value.push(
          {
            homePlacemant: kindOfHouse.value,
            animals: animal.value,
            prize: prize.value,
            currency: 'PLN',
          }
        )
        newData('');
        clearForm()
      }
      else{
        alert('Wrong Data! Please try again.');
      }
    } 

    function sortByHouseFun() {
      sortByHouse.value = sortByHouse.value == 2 ? 0 :sortByHouse.value + 1;
      sortByAnimals.value = 0;
      sortByPrize.value = 0;

      newData('')
    }

    function sortByAnimalsFun() {
      sortByAnimals.value = sortByAnimals.value == 2 ? 0 :sortByAnimals.value + 1;
      sortByHouse.value =0;
      sortByPrize.value = 0;
      newData('')
    }

    function sortByPrizeFun() {
      sortByPrize.value = sortByPrize.value == 2 ? 0 :sortByPrize.value + 1;
      sortByHouse.value = 0;
      sortByAnimals.value = 0;
      newData('')
    }

    function changePrize(code: string, value: number){
      allData.value.forEach(obj => {
        obj.prize = obj.prize * value;
        obj.currency = code;
      })
    }

    function changeCurrency(currency: string){
      axios.get(`https://api.currencyapi.com/v3/latest?apikey=ClKLR8qfxm9wOkIoZvlfTktlf14NM2s3h9tApw3q&currencies=PLN%2CEUR%2CUSD%2CGBP&base_currency=${baseCurrency.value}`)
      .then(({ data: { data }}) => {
        const curr = data[currency];
        changePrize(curr.code, curr.value)
        baseCurrency.value = currency;
        newData('')
      })
      .catch(error => console.log(error))
    }

    return {
      data,
      allData,
      search,
      kindOfHouse,
      animal,
      prize,
      sortByHouse,
      sortByAnimals,
      sortByPrize,
      baseCurrency,
      options,
      selected,
      newData,
      changeCurrency,
      addHouse,
      sortByHouseFun,
      sortByAnimalsFun,
      sortByPrizeFun
    }
  }
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .th {
    margin: 5px;
  }
  .data {
    margin-left: 10%;
    float: left;
  }
  .form {
    margin-right:10%;
    float: right;
  }
  .input {
    margin-top:5px;
    margin-bottom:5px;
  }
  #form-title {
    font-size:medium;
    font-weight: bold;
    margin-bottom: 5px;
  }
  #form-model{
    margin-top: 5px;
  }
</style>
