<template>
    <div class="complete">
        <div class="complete__cnt">
            <img src="https://static.cdn.24haowan.com/img/32/32152077606189344.png"
                class="complete__cnt__close"
                @click="hideComplete">
            <div class="complete__cnt__text">
                <div>恭喜你通关成功</div>
                <div class="complete__cnt__text--border">CONGRATULATIONS</div>
                <div>获广州K11艺术展览九折优惠券一张</div>
            </div>
            <img src="https://static.cdn.24haowan.com/img/32/32152083519644841.png"
                class="complete__cnt__get"
                @click="getCompletePrize">
        </div>
    </div>
</template>

<script>
export default {
    name: 'complete',
    methods: {
        hideComplete() {
            this.$bus.$emit('hideComplete');
        },
        getCompletePrize() {
            this.$bus.$emit('getCompletePrize');
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.complete {
    @include fullscreen;
    @include box;
    z-index: 100;
    background: $c-mask-blue;
    &__cnt {
        @include bg-contain;
        @include box;
        transform: translate(0, -30%);
        width: 86vw;
        color: $c-greenblue;
        position: relative;
        min-height: 40vh;
        background-size: 100% 100%;
        background-image: url('https://static.cdn.24haowan.com/img/32/32152077606093443.png');
        text-align: left;
        padding: 10vw;
        box-sizing: border-box;
        font-size: .2rem;
        font-family: 'CNB';
        &__close {
            position: absolute;
            top: 10vw;
            right: 6vw;
            width: 5vw;
            z-index: 1;
        }
        &__text {
            font-size: .22rem;
            font-weight: bold;
            text-align: center;
            &--border {
                border-bottom: .02rem dashed $c-greenblue;
                padding-bottom: .16rem;
                font-family: 'ENB';
            }
            div:last-child {
                font-size: .14rem;
                padding-top: .1rem;
            }
        }
        &__get {
            position: absolute;
            top: 100%;
            left: 50%;
            width: 40vw;
            transform: translate(-50%, .3rem);
        }
    }
}
</style>
