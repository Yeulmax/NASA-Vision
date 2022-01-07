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



      <div class="col-start-2 col-span-4 border-yellow-300 border-4 m-5">

        <!-- Rover -->
        <select name="roverSelect" id="roverSelect" v-model="selectedRover" class="m-4">
          <option value="" disabled selected>Rover</option>
          <option v-for="rover in roverList" v-bind:key="rover.name" v-bind:value="rover.name">{{ rover.name }}</option>
        </select>

        <!-- Camera -->
        <select name="cameraSelect" id="cameraSelect" v-model="selectedCamera" class="m-4">
          <option value="" disabled selected>Camera</option>
          <option v-for="camera in availableCameras" v-bind:key="camera.index" v-bind:value="camera">{{ camera }}</option>
        </select>

        <!-- Date -->
        <label for="datePicker">Date:</label>
        <input type="date" id="datePicker" name="trip-start" min="2012-08-06" max="2022-01-01" v-model="selectedDate">

        <!-- Photos -->
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

/* TODO
  -dynamic status
  -description cameras
  -max date
  -Move roverlist
  -plugin send last images
  -timelapse + mouse picking
 */
export default {
  name: "Main",
  data() {
    return {
      roverList: [
        {
          name: 'Spirit',
          launchDate: '2003-06-10',
          landingDate: '2004-01-04',
          maxDate: '2010-03-21',
          status: 'complete',
          cameraList: ['FHAZ', 'RHAZ', 'NAVCAM', 'PANCAM', 'MINITES']
        },
        {
          name: 'Opportunity',
          launchDate: '2003-07-07',
          landingDate: '2004-01-25',
          maxDate: '2018-06-11',
          status: 'complete',
          cameraList: ['FHAZ', 'RHAZ', 'NAVCAM', 'PANCAM', 'MINITES']
        },
        {
          name: 'Curiosity',
          launchDate: '2011-11-26',
          landingDate: '2012-08-06',
          maxDate: '2022-01-06',
          status: 'active',
          cameraList: ['FHAZ', 'RHAZ', 'MAST', 'CHEMCAM', 'MAHLI', 'MARDI', 'NAVCAM']
        },
        {
          name: 'Perseverance',
          launchDate: '2020-07-30',
          landingDate: '2021-02-18',
          maxDate: '2004-01-04',
          status: 'active',
          cameraList: ['EDL_RUCAM', 'EDL_RDCAM', 'EDL_DDCAM', 'EDL_PUCAM1',
            'EDL_PUCAM2', 'NAVCAM_LEFT', 'NAVCAM_RIGHT', 'MCZ_RIGHT',
            'MCZ_LEFT', 'FRONT_HAZCAM_LEFT_A', 'FRONT_HAZCAM_RIGHT_A',
            'REAR_HAZCAM_LEFT', 'REAR_HAZCAM_RIGHT', 'SKYCAM', 'SHERLOC_WATSON']
        }
      ],
      searchParameters: {
        'selectedRover'  : '',
        'selectedCamera' : '',
        'selectedDate'   : '',
      },
      selectedRover: '',
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
      test: [1,2,3,4,5],
      timelapse: "",
      counter: 0
    }
  },

  computed: {
    // Retourne les cameras associés au rover sélectionné
    availableCameras() {
      const selectedRover = this.roverList.filter(rover => rover.name === this.selectedRover)
      return selectedRover
    },
    axiosParams() {
      const params = new URLSearchParams();
      params.append('api_key', process.env.VUE_APP_API_KEY);
      return params;
    }
  },

  methods: {
    async fetchRoverData(){
      await axios
          .get(`http://localhost:8080/mars-photos/api/v1/rovers/curiosity/photos?camera=${this.selectedCamera}&earth_date=${this.selectedDate}`,
              {params: this.axiosParams})
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