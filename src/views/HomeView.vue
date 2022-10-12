<template>
  <div class="home">
    <div class="img">
      <img alt="Vue logo" src="../assets/logo.png">
    </div>
       <DataComponent/>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import DataComponent from '@/components/DataComponent.vue'; // @ is an alias to /src

export default defineComponent({
  name: 'HomeView',
  components: {
    DataComponent,
  },
});
</script>
<style scoped>
.img {
  text-align: center;
}
</style>
