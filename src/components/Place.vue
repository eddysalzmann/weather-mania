<template>
  <div :class="nightDay">
    <Weather :place-name="placeName" :place-data="placeData" />
    <button class="delete" @click="$emit('remove', place.id)">X</button>
  </div>
</template>

<script>
import Weather from '@/components/Weather'
import OpenWeatherMaps from '@/services/OpenWeatherMaps'

export default {
  components: {
    Weather
  },
  props: {
    place: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      forecast: new OpenWeatherMaps(this.place.name)
    };
  },
  computed: {
    placeName: function () {
      return this.$props.place.name
    },
    placeData: function () {
      return this.forecast
    },
    nightDay() {
      var now = Math.round(new Date().getTime()/1000);
      var sunrise = this.forecast.sunrise,
          sunset = this.forecast.sunset;

      return ( now > sunrise && now < sunset )? 'day':'night';
    }
  },

}
</script>

<style lang="scss" scoped>
  .delete{
display:none;
  }
</style>
