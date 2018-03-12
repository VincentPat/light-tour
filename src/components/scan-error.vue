<template>
    <div class="scan-error">
        <div class="scan-error__cnt">
            <img src="https://static.cdn.24haowan.com/img/32/32152077606189344.png"
                class="scan-error__cnt__close"
                @click="hideScanError">
            <div class="scan-error__cnt__text">
                <div>扫描地点错误</div>
                <div>请回到正确地点体验</div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'scan-error',
    methods: {
        hideScanError() {
            this.$bus.$emit('hideScanError');
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.scan-error {
    @include fullscreen;
    @include box;
    z-index: 100;
    background: $c-mask-blue;
    &__cnt {
        @include bg-contain;
        @include box;
        transform: translate(0, -20%);
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
        &__close {
            position: absolute;
            top: 10vw;
            right: 6vw;
            width: 5vw;
            z-index: 1;
        }
        &__text {
            font-size: .24rem;
            font-weight: bold;
            line-height: .44rem;
            text-align: center;
            div:first-child {
                border-bottom: .02rem dashed $c-greenblue;
            }
        }
    }
}
</style>
