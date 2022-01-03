<template>
  <header>

    <div class="grid grid-cols-6 border-solid border-red-500 border-4 h-screen">

      <!--
      <div class="col-start-2 col-span-4 border-green-500 border-4 m-5">
        <div class="flex justify-center items-center mt-16">
          <div class="text-center border-black border-4 w-2/3 h-96">
            <h1 class="text-3xl">DATE</h1>
            <p class="text-xl">Sub text</p>
          </div>
        </div>
      </div>
      -->



      <div class="col-start-2 col-span-4 border-yellow-300 border-4 m-5 ">


        <select name="cameraList" id="cameraList" v-model="selectedCamera" class="m-4">
          <option value="" selected="selected">Select Camera</option>
          <option value="FHAZ">FHAZ</option>
          <option value="RHAZ">RHAZ</option>
          <option value="MAST">MAST</option>
          <option value="CHEMCAM">CHEMCAM</option>
          <option value="MAHLI">MAHLI</option>
          <option value="MARDI">MARDI</option>
          <option value="NAVCAM">NAVCAM</option>
        </select>

        <label for="datePicker">Date:</label>

        <input type="date" id="datePicker" name="trip-start"
               min="2012-08-06" max="2022-01-01" v-model="selectedDate">



        <div class="grid grid-cols-3 border-blue-300 border-4">
          <h1 class="col-span-3 font-8xl font-extrabold text-red-500 text-left">{{ this.selectedCamera}}</h1>
          <div v-for="photo in roverData[selectedCamera]" :key="photo.id">
            <img :src="photo.img_src" alt="" class="border-black border-2 w-auto">
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
      selectedDate: '',
      selectedCamera: '',
      roverData: {
        'FHAZ'   : '',
        'RHAZ'   : '',
        'MAST'   : '',
        'CHEMCAM': '',
        'MAHLI'  : '',
        'MARDI'  : '',
        'NAVCAM' : '',
      },
      test: [],
      timelapse: "",
      counter: 0
    }
  },
  computed: {
    axiosParams() {
      const params = new URLSearchParams();
      params.append('api_key', process.env.VUE_APP_API_KEY);
      return params;
    }
  },
  methods: {

    async fetchRoverData(){
      await axios
          .get(`http://localhost:8080/mars-photos/api/v1/rovers/curiosity/photos?camera=${this.selectedCamera}&earth_date=${this.selectedDate}`, {params: this.axiosParams})
          .then(response => (this.roverData[this.selectedCamera] = response.data.photos))
          .catch(error => {
            console.log(error)
          });
    },

    concatPics(pics){
      pics.forEach(pic =>  this.FHAZ.push(pic))
    },

    startTimelapse(){
      setInterval(this.testTimelapse, 125)
    },

    testTimelapse(){
      if (this.counter < this.FHAZ.length){
        this.timelapse = this.FHAZ[this.counter].img_src
        this.counter++
        //console.log('N°', this.counter)
      }else{
        this.counter = 0
      }
    }
  },
  watch:{
    selectedCamera(){
      if (this.selectedDate) this.fetchRoverData()
    },
    selectedDate(){
      if (this.selectedCamera) this.fetchRoverData()
    }
  },
  async created() {

    //await this.fetchRoverData()

  },

};
</script>

<style>

</style>