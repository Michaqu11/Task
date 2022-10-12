<template>
  <div>
    <div class="data">
    <input
      v-model="search"
      placeholder="search"
      :style="{'margin-bottom':'10px'}"
      @input="newData(search)">
    <table class="table table-striped table-bordered">
    <thead>
      <tr>
        <th>Placment</th>
        <th>Animals</th>
        <th>Prize</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in data" :key="item.animals">
        <td>{{ item.homePlacemant  }}</td>  
        <td>{{ item.animals }}</td> 
        <td>{{ item.prize }}</td>  
      </tr>
    </tbody>
  </table>
  </div>
  <div class="form">
    <h3>Add new house</h3>

      <div >

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
        },
        {
          homePlacemant: 'tree',
          animals: 'birds',
          prize: 50, 
        },
        {
          homePlacemant: 'ground',
          animals: 'llamas',
          prize: 1500,
        }
      ]);

    const search = ref('');
    const kindOfHouse = ref('ground');
    const animal = ref('');
    const prize = ref(null)

    function newData(search: string){
      data.value = allData.value.filter(obj => {
        return obj.homePlacemant.includes(search) || obj.animals.includes(search) || String(obj.prize).includes(search)
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
          }
        )
        newData('');
        clearForm()
      }
      else{
        alert('Wrong Data! Please try again.');
      }
    } 

    onMounted(() => {
      newData('')
    });

    return {
      data,
      allData,
      search,
      kindOfHouse,
      animal,
      prize,
      newData,
      addHouse
    }
  }
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .th {
    margin: 1px;
  }
  .data {
    margin: 10px;
    float: left;
  }
  .form {
    margin-top: 10px;
    margin-left: 50%;
    float: left;
  }
  .input {
    margin-top:5px;
     margin-bottom:5px;
  }
</style>
