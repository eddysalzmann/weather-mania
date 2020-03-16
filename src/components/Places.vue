<template>

	<div>
			<div v-if="places.length">
					<div class="slider">
							<div class="slider__list" ref="list" v-pan="onPan" :data-active-slide="activeSlide">
									<Place v-for="(place, i) in places.slice().reverse()" :class="'slider__item'" :key="`${i}-${place.id}`" :place="place" @remove="removePlace" />
							</div>
							<div class="slider__pagination" v-if="places.length">
									<div class="pagination_item" v-for="(place, i) in places" :key="`${i}-${place.id}`" @remove="removePlace" />
							</div>
					</div>
			</div>

	</div>

</template>

<script>

import Vue from 'vue'
import Hammer from 'hammerjs'
import gsap from 'gsap'
import Place from './Place.vue'
import router from '../router'

Vue.directive("pan", {
    bind: function(el, binding) {
        if (typeof binding.value === "function") {
            const mc = new Hammer(el);
            mc.get("pan").set({
                direction: Hammer.DIRECTION_ALL,
                preventDefault: true,
                domEvents: true
            });
            mc.on("panstart pan", binding.value);
        }
    }
});

export default {
    components: {
        Place
    },
    data() {
        return {
            newPlace: '',
            places: [],
            currentOffset: 0,
            currentCardOffset: 0,
            dragOffsetVal: 0,
            count: 0,
            newOffsetWidth: 0,
            newScrollWidth: 0,
            activeSlide: 0,
            weatherIconsWrapper: '',
            activeSliderItem: '',
            activeSliderItemName: '',
            activeSliderItemValue: '',
            hammer_horizontal: false,
            detailsCard: '',
						windDeg: 0

        }
    },
    computed: {
        overflowRatio() {
                return this.newScrollWidth / this.newOffsetWidth;
            },
            itemWidth() {
                return this.newScrollWidth / this.count;
            }
    },
    watch: {
        places: {
            handler() {
                    localStorage.setItem('places', JSON.stringify(this.places));
                },
                deep: true
        }
    },
    methods: {
        removePlace(idToRemove) {
                this.count = this.count - 1;
                var self = this;
                setTimeout(function() {
                    self.newScrollWidth = self.$refs.list.scrollWidth;
                    self.newOffsetWidth = self.$refs.list.offsetWidth;
                }, 300);
                this.places = this.places.filter(place => {
                    return place.id !== idToRemove
                })
            },
            iconMover(input) {
                let counter = ((360 / this.places.length) / 100);
                return counter * input;
            },
            changePagination(slide) {
                let paginationItem = document.getElementsByClassName("pagination_item");
                for (let i = 0; i < paginationItem.length; i++) {
                    paginationItem[i].classList.remove('active');
                }
                paginationItem[slide].classList.add('active');
            },
            onPan(e) {

                if (e.type == 'panstart') {
                    if (e.additionalEvent === 'panleft' || e.additionalEvent === 'panright') {
                        this.hammer_horizontal = true;
                    }
                    //this.detailsCard = this.activeSliderItem.getElementsByClassName("details-card")[0];
                }

                if (this.hammer_horizontal) {
                    const dragOffset = 100 / this.itemWidth * e.deltaX / this.count * this.overflowRatio;
                    const transform = this.currentOffset + dragOffset;
                    this.$refs.list.style.setProperty("--x", transform);

                    gsap.to(this.activeSliderItemValue, 0.4, {
                        left: dragOffset * 4 + "px"
                    })
                    gsap.to(this.activeSliderItemName, 0.4, {
                        left: dragOffset * 3 + "px"
                    })

                    if (e.isFinal) {
                        this.hammer_horizontal = false;

                        this.currentOffset = transform;
                        const maxScroll = 100 - this.overflowRatio * 100;
                        let finalOffset = this.currentOffset;

                        if (this.currentOffset <= maxScroll) {
                            finalOffset = maxScroll;
                        } else if (this.currentOffset >= 0) {
                            finalOffset = 0;
                        } else {
                            const index = this.currentOffset / this.overflowRatio / 100 * this.count;
                            if (dragOffset > -10 && dragOffset < 10) {
                                const currIndex = e.deltaX <= 0 ? Math.ceil(index) : Math.floor(index);
                                finalOffset = 100 * this.overflowRatio / this.count * currIndex;
                            } else {
                                const nextIndex = e.deltaX <= 0 ? Math.floor(index) : Math.ceil(index);
                                finalOffset = 100 * this.overflowRatio / this.count * nextIndex;
                            }
                        }

                        this.activeSlide = Math.abs(finalOffset / 100);
                        this.activeSliderItem = document.getElementsByClassName("slider__item")[this.activeSlide];
                        this.activeSliderItemName = this.activeSliderItem.getElementsByClassName("weather__icon")[0];
                        this.activeSliderItemValue = this.activeSliderItem.getElementsByClassName("weather__value")[0];
                        this.detailsCard = this.activeSliderItem.getElementsByClassName("details-card")[0];

                        this.changePagination(this.activeSlide);

												let wind = this.activeSliderItem.getElementsByClassName("weather")[0].getAttribute('data-wind');
												document.getElementById('app__nav__wind').style.transform = 'rotate('+wind+'deg)';

                        // bounce back animation

                        //Slides
                        gsap.fromTo(
                            this.$refs.list,
                            0.4, {
                                '--x': this.currentOffset
                            }, {
                                '--x': finalOffset,
                                onComplete: () => {
                                    this.currentOffset = finalOffset;
                                }
                            }
                        );

                        gsap.to(this.activeSliderItemValue, 0.45, {
                            left: "0px"
                        })
                        gsap.to(this.activeSliderItemName, 0.4, {
                            left: "0px"
                        })

                        if (this.activeSliderItem.classList.contains("night")) {
                            document.body.classList.remove("day");
                            document.body.classList.add("night");
                        } else {
                            document.body.classList.remove("night");
                            document.body.classList.add("day");
                        }
                    }
                }
            }
    },
    async mounted() {

        //Refresh data
        setTimeout(function() {
            location.reload();
        }, 600000)

        if (localStorage.getItem('places')) {
            this.places = JSON.parse(localStorage.getItem('places'));
            this.count = this.places.length;
        }

				if (this.places.length == 1) {
            document.body.classList.add('single-place');
        }else{
					document.body.classList.remove('single-place');
				}

        await this.$nextTick()

				if (this.places.length == 0) {
						document.body.classList.add('no-place');
						router.push('/search')
            return;
        }else{
						document.body.classList.remove('no-place');
				}

        this.newScrollWidth = this.$refs.list.scrollWidth;
        this.newOffsetWidth = this.$refs.list.offsetWidth;

        let self = this;
        setTimeout(function() {

            self.activeSliderItem = document.getElementsByClassName("slider__item")[self.activeSlide];
            self.activeSliderItemName = self.activeSliderItem.getElementsByClassName("weather__icon")[0];
            self.activeSliderItemValue = self.activeSliderItem.getElementsByClassName("weather__value")[0];

            if (self.activeSliderItem.classList.contains("night")) {
                document.body.classList.remove("day");
                document.body.classList.add("night");
            } else {
                document.body.classList.remove("night");
                document.body.classList.add("day");
            }

            self.detailsCard = self.activeSliderItem.getElementsByClassName("details-card")[0];
            self.changePagination(self.activeSlide);

						let wind = self.activeSliderItem.getElementsByClassName("weather")[0].getAttribute('data-wind');
						document.getElementById('app__nav__wind').style.transform = 'rotate('+wind+'deg)';

        }, 400)
    }
}

</script>

<style lang="scss" scoped>

@import '@/variables.scss';
h1 {
    text-transform: uppercase;
    color: $white;
    padding: 0 2rem;
    margin-bottom: 1rem;
}

p {
    margin-top: 0;
    margin-bottom: 2rem;
		color:$white;
}

button {
    width: 80px;
    height: 80px;
    display: block;
    font-size: 4rem;
    padding: 0;
    line-height: 0;
    overflow: hidden;
    padding-bottom: 7px;
    color: #fff;
}

.empty_places {
    display: flex;
    width: 100%;
    height: 90vh;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    svg {
        width: 27vh;
    }
}

</style>
<style lang="scss">

@import '@/variables.scss';

.slider {
    width: 100%;
    height: 100vh;
    overflow: visible;
    position: relative;
    overflow: hidden;
    white-space: nowrap;
    &__list {
        display: flex;
        width: 100%;
        height: 100%;
        position: relative;
        z-index: 5;
        backface-visibility: hidden;
        transform: translateX(calc(var(--x, 0) * 1%));
        .details-card {
            backface-visibility: hidden;
            transform: translateY(calc(var(--y, 0) * 1px));
        }
    }
    &__item {
        flex: 0 0 100%;
        display: flex;
				padding:2rem;
        justify-content: center;
        align-items: center;
        box-sizing: border-box;
        text-align: center;
        transition: opacity 0.15s ease;
        overflow: hidden;
        height: 100vh;
				white-space: normal;
        > div {
            display: flex;
            height: 100%;
            text-align: center;
            justify-content: center;
            align-items: center;
            position: relative;
            width: 80%;
						margin: 0 20px;
						padding: 0 20px;
        }
    }
    &__pagination {
        display: flex;
        align-items: center;
        justify-content: center;
        position: fixed;
        bottom: 12vh;
        left: 0;
        right: 0;
        z-index: 2;
				.single-place &{
					display:none;
				}
        .pagination_item {
            width: 7px;
            height: 7px;
            border-radius: 50%;
            display: block;
            margin: 0 7px;
            background: $secondary-light;
            .night & {
                background: $primary-dark;
            }
            &.active {
                background: $secondary;
                .night & {
                    background: $primary;
                }
            }
        }
    }
    .day & {
        svg {
            path {
                fill: $secondary
            }
        }
    }
    .night & {
        svg {
            path {
                fill: $primary
            }
        }
    }
}

.weather-icons {
    position: fixed;
    pointer-events: none;
    bottom: 0;
    height: 94vh;
    width: 100%;
    transform-origin: bottom;
    .weather-icon {
        height: 100%;
        width: 26vh;
        position: absolute;
        left: 50%;
        margin-left: -13vh;
        top: 0;
        transform-origin: bottom;
        svg {
            max-width: 50vw;
            path {
                fill: $secondary;
                .night & {
                    fill: $primary;
                }
            }
        }
        img {
            width: 100%;
            height: auto;
            .night & {}
        }
    }
}

</style>
