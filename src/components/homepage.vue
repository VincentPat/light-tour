<template>
    <div class="homepage">
        <img class="homepage__logo"
            src="https://static.cdn.24haowan.com/img/32/32152051868757390.png">
        <img src="https://static.cdn.24haowan.com/img/32/32152109741297338.png"
            class="homepage__light">
        <img src="https://static.cdn.24haowan.com/img/32/32152068822683762.png"
            class="homepage__line">
        <img src="https://static.cdn.24haowan.com/img/32/3215210860219432.png"
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
export default {
    name: 'homepage',
    data() {
        return {
            showGift: false
        };
    },
    methods: {
        getGift() {
            this.$bus.$emit('showComplete');
        }
    },
    mounted() {
        this.$bus.$on('showGiftButton', () => {
            this.showGift = true;
        });
        this.$bus.$on('hideGiftButton', () => {
            this.showGift = false;
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.homepage {
    @include fullscreen;
    @include bg-cover;
    background-color: $c-blue;
    &__logo {
        position: absolute;
        top: 10vw;
        right: 6vw;
        width: 16vw;
    }
    &__light {
        position: absolute;
        left: 0;
        top: 50%;
        width: 100vw;
        transform: translate(0, -50%);
    }
    &__line {
        position: absolute;
        bottom: 18vh;
        right: 0;
        width: 90vw;
    }
    &__title {
        position: absolute;
        left: 6vw;
        top: 50%;
        width: 80vw;
        transform: translate(0, -70%);
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

