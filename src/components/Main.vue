<template>
  <header>
    <div class="grid grid-cols-6 border-solid ">
      <div class="col-start-2 col-span-4 border-black border-4 h-96">
        <div class="grid grid-cols-3">

          <div class="col-start-2 border-black border-4 m-4 h-60">
            Test
          </div>

        </div>
      </div>
    </div>
  </header>
</template>

<script>
import axios from "axios";

export default {
  name: "Main",
  data() {
    return {
      API_KEY: process.env.VUE_APP_API_KEY,
      test: {}
    }
  },
  computed: {
    axiosParams() {
      const params = new URLSearchParams();
      params.append('key', this.API_KEY);
      params.append('steamids', this.STEAM_ID);
      return params;
    }
  },

  async created(){

    await axios
        .get(`http://localhost:8080/ISteamUser/GetPlayerSummaries/v2`,{params: this.axiosParams})
        .then(response => (this.user = response.data.response.players[0]))
        .catch(error => {console.log(error)});

    /*
    await axios
        .get(`http://localhost:8080/todos/1`)
        .then(response => (this.username = response.data))
        .catch(error => {console.log(error)});
*/
  }


};
</script>

<style>

</style>