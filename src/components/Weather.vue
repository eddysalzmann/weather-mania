<template>

  <div class="weather" :data-wind="this.openweatherdata.wind">
      <div class="weather__icon" v-html="this.openweatherdata.weatherIcon"/>
      <ResizeText class="weather__location">{{this.openweatherdata.location}}</ResizeText>
      <div class="weather__value">
        {{ (scaleSymbol === 'C')? this.openweatherdata.temperatureValue : fValue }}<a href="#" @click.prevent="toggleTemperatureType">&deg;{{ scaleSymbol }}</a>
      </div>
      <p class="weather__description">
        {{this.openweatherdata.description}}
      </p>
  </div>

</template>

<script>
import ResizeText from '@/components/ResizeText';

export default {
    name: 'Weather',
    components: {
        ResizeText
    },
    props: {
      'place-name' :{
        type: String,
        required: true
      },
      'place-data' :{
        type: Object,
        required: true
      }
    },
    data() {
        return {
            openweatherdata: this.placeData,
            scale: 'Celcius',

        }
    },
    computed: {
        scaleSymbol() {
            return this.scale.charAt(0);
        },

        fValue() {
            return this.toFahrenheit(this.openweatherdata.temperatureValue);
        }

    },
    methods: {
        toFahrenheit(value) {
            return Math.floor((value * 1.8) + 32);
        },
        toggleTemperatureType() {
            (this.scale === 'Celcius')? this.scale = 'Fahrenheit' : this.scale = 'Celcius';
        }

    }
}
</script>

<style lang="scss">

@import '@/variables.scss';

.weather {

    display:flex;
    flex-direction:column;

    &__icon{
        position:relative;
        margin-top: -4rem;
        svg {
          width: 100%;
          transform: scale(1.5);
        }
    }

    &__location {
        text-transform: uppercase;
        font-weight: bold;
        color:$white!important;
        z-index: 10;
        line-height:0.8;
    }

    &__value {
        font-size: 8vh;
        font-weight: 700;
        letter-spacing: -.05em;
        margin-top: 1rem;
    }

    &__description:first-letter{
      text-transform:uppercase;
    }

}

</style>
