<template>
    <div class="home" @touchmove.stop.prevent>
        <swiper :options="swiperOption"
            ref="swiper"
            class="home__swiper"
            :style="swiperInteractive">
            <swiper-slide>
                <homepage></homepage>
            </swiper-slide>
            <swiper-slide>
                <rule></rule>
            </swiper-slide>
            <swiper-slide></swiper-slide>
        </swiper>
        <div class="home__music">
            <v-music-switch
                @switch="switchMusic"
                :on="musicOn"
                :imgOn="imgOn"
                :imgOff="imgOff">
            </v-music-switch>
        </div>
        <div class="home__floors"
            :class="{ show: showFloors }"
            @touchmove.stop>
            <img src="https://static.cdn.24haowan.com/img/32/32152056921844150.png"
                class="home__floors__bg"
                @load="bgLoad">
            <floor1 :style="getFloorHeight(1)"></floor1>
            <floor2 :style="getFloorHeight(2)"></floor2>
            <floor3 :style="getFloorHeight(3)"></floor3>
            <floor4 :style="getFloorHeight(4)"></floor4>
            <floor5 :style="getFloorHeight(5)"></floor5>
            <floor6 :style="getFloorHeight(6)"></floor6>
        </div>
    </div>
</template>

<script>
import vMusicSwitch from '@/components/common/v-music-switch';
import homepage from '@/components/homepage';
import rule from '@/components/rule';
import floor1 from '@/components/floor1';
import floor2 from '@/components/floor2';
import floor3 from '@/components/floor3';
import floor4 from '@/components/floor4';
import floor5 from '@/components/floor5';
import floor6 from '@/components/floor6';

export default {
    name: 'home',
    components: {
        vMusicSwitch,
        homepage,
        rule,
        floor1,
        floor2,
        floor3,
        floor4,
        floor5,
        floor6,
    },
    computed: {
        swiper() {
            return this.$refs.swiper.swiper;
        },
        swiperInteractive() {
            return this.showSwiper ? null : 'pointer-events: none;';
        }
    },
    data() {
        return {
            musicOn: true,
            imgOn: 'https://static.cdn.24haowan.com/img/32/32152051868661185.png',
            imgOff: 'https://static.cdn.24haowan.com/img/32/32152051868551902.png',
            swiperOption: {
                direction: 'vertical',
                on: {
                    slideNextTransitionStart: () => {
                        this.$bus.$emit('slideChange', {
                            activeIndex: this.swiper.activeIndex,
                            callback: () => {
                                this.showFloors = true;
                            }
                        });
                    },
                    slideNextTransitionEnd: () => {
                        if (this.swiper.activeIndex === 2) {
                            this.showSwiper = false;
                        }
                    }
                }
            },
            showFloors: false,
            showSwiper: true,
            lastScroll: 0,
        };
    },
    methods: {
        switchMusic() {
            this.$bus.$emit('switchMusic');
        },
        bgLoad() {
            this.$bus.$emit('progress', 21);
        },
        getFloorHeight(n) {
            const height = window.innerWidth / (375 / 607);
            let top = 0;
            switch (n) {
            case 1:
                top = 0;
                break;
            case 2:
                top = height * 0.858;
                break;
            case 3:
                top = height * 1.784;
                break;
            case 4:
                top = height * 2.885;
                break;
            case 5:
                top = height * 3.868;
                break;
            case 6:
                top = height * 4.843;
                break;
            default:
                break;
            }
            return `height: ${height}px;top: ${top}px;`;
        }
    },
    mounted() {
        this.$bus.$on('switchMusic', () => {
            this.musicOn = !this.musicOn;
        });
        const floors = document.querySelectorAll('.home__floors')[0];
        floors.addEventListener('scroll', () => {
            if (floors.scrollTop < -40) {
                this.showFloors = false;
                this.showSwiper = true;
                this.swiper.slideTo(1);
            }
            this.lastScroll = floors.scrollTop;
        });
        window.swiper = this.swiper;
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.home {
    @include fullscreen;
    background-color: $c-blue;
    opacity: 0;
    &.show {
        opacity: 1;
    }
    .swiper-wrapper {
        width: 100vw;
        height: 100vh;
    }
    &__music {
        z-index: 2;
        position: absolute;
        top: 6vw;
        left: 6vw;
        width: 10vw;
        height: 10vw;
    }
    &__floors {
        @include fullscreen;
        transition: all linear .5s;
        opacity: 0;
        overflow-y: auto;
        overflow-x: hidden;
        -webkit-overflow-scrolling: touch;
        &.show {
            opacity: 1;
        }
        &__bg {
            width: 100%;
        }
    }
}
</style>
