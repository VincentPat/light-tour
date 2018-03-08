<template>
    <div class="homepage">
        <img class="homepage__logo"
            src="https://static.cdn.24haowan.com/img/32/32152051868757390.png">
        <div class="homepage__music">
            <v-music-switch
                @switch="switchMusic"
                :on="musicOn"
                :imgOn="imgOn"
                :imgOff="imgOff">
            </v-music-switch>
        </div>
        <img src="https://static.cdn.24haowan.com/img/32/32152051869532387.png"
            class="homepage__title">
        <div class="homepage__gift"
            v-show="showGift"
            @click="getGift">
            <div class="homepage__gift__text">领取通关礼品</div>
        </div>
        <img src="https://static.cdn.24haowan.com/img/32/32152051868785613.png"
            class="homepage__slider wanderVer">
    </div>
</template>

<script>
import vMusicSwitch from '@/components/common/v-music-switch';

export default {
    name: 'homepage',
    components: {
        vMusicSwitch
    },
    data() {
        return {
            musicOn: true,
            imgOn: 'https://static.cdn.24haowan.com/img/32/32152051868661185.png',
            imgOff: 'https://static.cdn.24haowan.com/img/32/32152051868551902.png',
            showGift: true
        };
    },
    methods: {
        switchMusic() {
            this.$bus.$emit('switchMusic');
        },
        getGift() {
            this.$bus.$emit('getGift');
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
@import '../../style/mixin.scss';
@import '../../style/color.scss';

.homepage {
    @include fullscreen;
    @include bg-cover;
    background-image: url('https://static.cdn.24haowan.com/img/32/32152051868257666.png');
    &__logo {
        position: absolute;
        top: 10vw;
        right: 6vw;
        width: 16vw;
    }
    &__music {
        position: absolute;
        top: 6vw;
        left: 6vw;
        width: 10vw;
        height: 10vw;
    }
    &__title {
        position: absolute;
        left: 6vw;
        top: 24vw;
        width: 80vw;
    }
    &__gift {
        @include bg-contain;
        position: absolute;
        bottom: 12vw;
        right: 4vw;
        width: 22vw;
        height: 22vw;
        background-image: url('https://static.cdn.24haowan.com/img/32/32152051869483812.png');
        &__text {
            color: $c-lightblue;
            font-size: .12rem;
            text-align: center;
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            transform: translate(0, 100%) scale(0.8, 0.8);
        }
    }
    &__slider {
        position: absolute;
        bottom: 5vh;
        left: 46vw;
        width: 8vw;
    }
}
</style>

