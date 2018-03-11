<template>
    <div class="home">
        <swiper :options="swiperOption"
            ref="swiper"
            class="home__swiper">
            <!-- slides -->
            <swiper-slide>
                <homepage></homepage>
            </swiper-slide>
            <swiper-slide>
                <rule></rule>
            </swiper-slide>
            <swiper-slide>
                <floor1></floor1>
            </swiper-slide>
            <swiper-slide>
                <floor2></floor2>
            </swiper-slide>
            <swiper-slide>
                <floor3></floor3>
            </swiper-slide>
            <swiper-slide>
                <floor4></floor4>
            </swiper-slide>
            <swiper-slide>
                <floor5></floor5>
            </swiper-slide>
            <swiper-slide>
                <floor6></floor6>
            </swiper-slide>
            <img src="https://static.cdn.24haowan.com/img/32/32152056921844150.png"
                class="home__swiper__bg"
                :style="bgStyle">
        </swiper>
        <div class="home__music">
            <v-music-switch
                @switch="switchMusic"
                :on="musicOn"
                :imgOn="imgOn"
                :imgOff="imgOff">
            </v-music-switch>
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
        bgStyle() {
            return `top: ${window.innerHeight * 2}px`;
        }
    },
    data() {
        return {
            musicOn: true,
            imgOn: 'https://static.cdn.24haowan.com/img/32/32152051868661185.png',
            imgOff: 'https://static.cdn.24haowan.com/img/32/32152051868551902.png',
            swiperOption: {
                direction: 'vertical'
            }
        };
    },
    methods: {
        switchMusic() {
            this.$bus.$emit('switchMusic');
        }
    },
    mounted() {
        this.$bus.$on('switchMusic', () => {
            this.musicOn = !this.musicOn;
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.home {
    @include fullscreen;
    background-color: $c-blue;
    .swiper-wrapper {
        width: 100vw;
        height: 100vh;
    }
    &__swiper {
        &__bg {
            z-index: -1;
            position: absolute;
            left: 50%;
            height: 600vh;
            transform: translate(-50%, 0);
        }
    }
    &__music {
        z-index: 2;
        position: absolute;
        top: 6vw;
        left: 6vw;
        width: 10vw;
        height: 10vw;
    }
}
</style>
