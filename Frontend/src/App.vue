<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { v4 as uuidv4 } from 'uuid';

export default {
  setup() {
    const inputData = ref('');
    const storagedata = ref([]);

    const insertdata = async (userdata) => {
      try {
        const id = uuidv4();
        console.log(id)
        const result = await axios.post('http://localhost:8080/insertdata', {id: id, userdata: userdata });
        console.log(result);
        if (result.data.success) {
          const newdata = await getData();
          storagedata.value = newdata;
        }
      } catch (error) {
        console.error('Error inserting data:', error);
      }
    };

    const submitData = () => {
      insertdata(inputData.value);
      inputData.value = '';  // Clear input after submission
    };

    const removeData = async (id) => {
      try {
        const result = await axios.delete('http://localhost:8080/removedata/${id}')
        console.log('ID:', id);
        console.log(result)
        return result.data.storagedata;
      } catch (error) {
        console.log('Feil ved henting av data:', error);
        return[];
      }
    }

    const getData = async () => {
      try {
        const result = await axios.get('http://localhost:8080/getdata');
        console.log(result);
        return result.data.storagedata;
      } catch (error) {
        console.error('Error getting data:', error);
        return [];
      }
    };

    onMounted(async () => {
      storagedata.value = await getData();
    });

    return {
      submitData,
      inputData,
      storagedata,
      removeData
    };
  },
};
</script>

<template>
  <h1>Vue adøaldkøla</h1>
  <input v-model="inputData">
  <button @click="submitData">Submit</button>
  <div v-for="(item, outerIndex) in storagedata" :key="'outer' + outerIndex">
    <ul>
      <li :id="item.compdata.userdataid">
        {{ item.compdata.userdata }}
        <button @click="removeData(item.compdata.userdataid)">Fjern</button>
      </li>
    </ul>
  </div>
</template>
