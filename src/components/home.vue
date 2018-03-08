<template>
    <div class="home">
        <swiper :options="swiperOption"
            ref="swiper">
            <swiper-slide>
                <homepage></homepage>
            </swiper-slide>
            <swiper-slide>I'm Slide 2</swiper-slide>
            <swiper-slide>I'm Slide 3</swiper-slide>
            <swiper-slide>I'm Slide 4</swiper-slide>
            <swiper-slide>I'm Slide 5</swiper-slide>
            <swiper-slide>I'm Slide 6</swiper-slide>
            <swiper-slide>I'm Slide 7</swiper-slide>
        </swiper>
    </div>
</template>

<script>
import homepage from '@/components/pages/homepage';

export default {
    name: 'home',
    components: {
        homepage
    },
    computed: {
        swiper() {
            return this.$refs.swiper.swiper;
        }
    },
    data() {
        return {
            swiperOption: {
                direction: 'vertical'
            }
        };
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.home {
    @include fullscreen;
    @include bg-cover;
    background-color: $c-blue;
    .swiper-wrapper {
        width: 100vw;
        height: 100vh;
    }
}
</style>
