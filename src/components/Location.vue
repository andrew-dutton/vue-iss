<template>
  <div class="location">
    <h1>ISS LOCATION</h1>
    <hr>
    <b-button variant="dark" @click="getLocation">Click to refresh</b-button>
    <hr>
    <h1 class="brown">{{ position.countryName }}</h1>
    <h1 class="blue">{{ position.oceanName }}</h1>
    <h3 class="white">{{ position.unavailableMessage }}</h3>
  </div>
</template>

<script>
export default {
  name: 'Location',
  data() {
    return {
      position: {
        latitude: 'loading...',
        longitude: 'loading...',
        countryName: '',
        oceanName: '',
        url: 'https://api.opencagedata.com/geocode/v1/json?key=',
        key: 'API_KEY',
        joiner: "%2C",
        urlEnd: "&pretty=1",
        unavailableMessage: ''
      }
    }
  },
  methods: {
    getLocation() {
      fetch('https://api.wheretheiss.at/v1/satellites/25544')
      .then(response => response.json())
      .then(response => {
        this.position.latitude = parseFloat(response.latitude).toFixed(5),
        this.position.longitude = parseFloat(response.longitude).toFixed(5)
        fetch(this.position.url 
          + this.position.key 
          + this.position.latitude 
          + this.position.joiner 
          + this.position.longitude 
          + this.position.urlEnd
          )
          .then(data => data.json())
          .then(
          (data) => {
            if (data.total_results === 0) {
              this.position.unavailableMessage = 'Above International Waters'
              this.position.countryName = ''
              this.position.oceanName = ''
            } else if (!!data.results[0].components.country) {
              this.position.countryName = data.results[0].components.country
              this.position.oceanName = ''
              this.position.unavailableMessage = ''
            } else {
              this.position.oceanName = data.results[0].components.body_of_water
              this.position.countryName = ''
              this.position.unavailableMessage = ''
            }
          })
        })
        .catch(err => {
          console.log(err)
        })
    }
  },
  created() {
    this.getLocation()
  }
}
</script>

<style>
  .location {
    text-align: center;
    color: rgba(255, 255, 255, 0.788);
  }
  h1.brown {
    color: brown;
    font-size: 70px;
  }
  h1.blue {
    color: blue;
    font-size: 70px;
  }
  h3.white {
    color: whitesmoke;
    font-size: 70px;
  }
</style>
