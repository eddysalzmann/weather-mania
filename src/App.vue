<template>

<div id="app">

    <div id="app__nav">
        <a @click="removePlace" id="app__nav__remove" v-bind:class="{ hide: isSearch }" href="javascript:;">
          Remove
        </a>

        <div v-bind:class="{ hide: isSearch }" id="app__nav__wind">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 37 37">  <g fill="none" fill-rule="evenodd" class="page-1">    <g fill="#FFF" fill-rule="nonzero" class="page-1__03-day" transform="translate(-169 -21)">      <g class="page-1__03-day__wi-wind-deg" transform="translate(169.4 21)"><path d="M0.184000018,18.4000003 C0.184000018,15.1360002 1.00000003,12.1120002 2.61600005,9.34400014 C4.23200008,6.5760001 6.42400011,4.38400007 9.19200015,2.76800004 C11.9600002,1.15200002 14.9680002,0.352000005 18.2160003,0.352000005 C20.6480003,0.352000005 22.9840004,0.832000012 25.2080004,1.77600003 C27.4320004,2.72000004 29.3360005,4.01600006 30.9520005,5.61600008 C32.5680005,7.21600011 33.8480005,9.13600014 34.7920005,11.3760002 C35.7360005,13.6160002 36.2160006,15.9360002 36.2160006,18.4000003 C36.2160006,20.8320003 35.7360005,23.1680003 34.7920005,25.3920004 C33.8480005,27.6160004 32.5520005,29.5360004 30.9520005,31.1360005 C29.3520005,32.7360005 27.4320004,34.0160005 25.2080004,34.9600005 C22.9840004,35.9040005 20.6640003,36.3840005 18.2160003,36.3840005 C15.7680002,36.3840005 13.4160002,35.9040005 11.1920002,34.9600005 C8.96800015,34.0160005 7.04800012,32.7200005 5.4320001,31.1200005 C3.81600007,29.5200004 2.55200005,27.6000004 1.59200004,25.3920004 C0.632000024,23.1840003 0.184000018,20.8480003 0.184000018,18.4000003 Z M4.15200008,18.4000003 C4.15200008,22.1920003 5.5280001,25.4880004 8.29600014,28.2880004 C11.0640002,31.0560005 14.3600002,32.4320005 18.2160003,32.4320005 C20.7440003,32.4320005 23.0960004,31.8080005 25.2400004,30.5440005 C27.3840004,29.2800004 29.1120004,27.5840004 30.3760005,25.4240004 C31.6400005,23.2640003 32.2800005,20.9280003 32.2800005,18.4000003 C32.2800005,15.8720002 31.6400005,13.5200002 30.3760005,11.3600002 C29.1120004,9.20000014 27.4000004,7.48800011 25.2400004,6.22400009 C23.0800004,4.96000007 20.7440003,4.33600006 18.2160003,4.33600006 C15.6880002,4.33600006 13.3360002,4.96000007 11.1920002,6.22400009 C9.04800015,7.48800011 7.32000012,9.20000014 6.0400001,11.3600002 C4.76000009,13.5200002 4.15200008,15.8720002 4.15200008,18.4000003 Z M11.9760002,27.7600004 L17.9760003,6.7840001 C17.9920003,6.6240001 18.0720003,6.5440001 18.2160003,6.5440001 C18.3600003,6.5440001 18.4400003,6.6240001 18.4560003,6.7840001 L24.4400004,27.7600004 C24.5040004,27.9360004 24.4880004,28.0640004 24.4080004,28.1600004 C24.3280004,28.2560004 24.2000004,28.2560004 24.0240004,28.1600004 L18.4720003,26.0800004 C18.3120003,26.0160004 18.1520003,26.0160004 18.0080003,26.0800004 L12.4080002,28.1600004 C12.2480002,28.2560004 12.1360002,28.2560004 12.0720002,28.1600004 C12.0080002,28.0640004 11.9440002,27.9200004 11.9760002,27.7600004 Z" class="page-1__03-day__wi-wind-deg__shape"/></g></g></g></svg>
        </div>

        <router-link :to="routerLink" id="app__nav__link">
            {{linkText}}
        </router-link>

    </div>
    <transition name="fade">
        <router-view/>
    </transition>
</div>

</template>

<script>

export default {
    data() {
        return {
            isSearch: false,
            routerLink: "/search",
            linkText: "Add"
        }
    },
    methods:{
      removePlace(){
        let slider = document.getElementsByClassName("slider__list")[0],
            slider_active_val = slider.getAttribute('data-active-slide'),
            slider_active = document.getElementsByClassName("slider__item")[slider_active_val];

        slider_active.getElementsByClassName("delete")[0].click();
        location.reload();

      }
    },
    updated() {
      if(this.$route.path == "/search"){
        this.isSearch = true;
        this.routerLink = "/"
        this.linkText = "Close"
      }else{
        this.isSearch = false;
        this.routerLink = "/search"
        this.linkText = "Add"
      }
    },
}
</script>

<style lang="scss">

@import '@/variables.scss';

#app {
    height: 100vh;
    overflow: hidden;
    &__nav {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        display: flex;
        justify-content: space-between;
        font-size: 14px;
        text-transform: uppercase;
        letter-spacing: 1px;
        font-weight: bold;
        padding: 30px 20px;
        z-index: 20;
        align-items: center;
        .no-place &{
          display:none;
        }
        &__link,&__remove,&__wind{
            transition:all 200ms ease-in-out;
        }

        &__wind{
          position:absolute;
          left:50%;
          margin-left:-10px;
        }

        svg {
            width: 60px;
            display:block;
            padding: 10px;
            path {
                fill: $white
            }
        }

        .hide{
          opacity:0;
          pointer-events:none;
        }
    }
}

/** Transitions **/

.fade-enter-active,
.fade-leave-active,
.fade-enter,
.fade-leave-to {
    transition: all .5s;
}

.fade-enter,
.fade-leave-to{
    opacity: 0;
}

.fade-enter-active {
    transition-property: opacity;
    transition-duration: 400ms;
}

.fade-leave-active {
    transition-property: opacity;
    transition-duration: 400ms;
}

.fade-enter-active {
    transition-delay: 500ms;
}

.fade-enter,
.fade-leave-active {
    opacity: 0
}

</style>
