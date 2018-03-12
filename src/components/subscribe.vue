<template>
    <div class="subscribe" @click="hideSubscribe">
        <div class="subscribe__cnt">
            <img src="https://static.cdn.24haowan.com/img/32/32152077503637698.png"
                class="subscribe__cnt__qrcode">
            <div class="subscribe__cnt__text">
                <p>关注广州K11</p>
                <p>获取最新开业活动信息</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'subscribe',
    methods: {
        hideSubscribe() {
            this.$bus.$emit('hideSubscribe');
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.subscribe {
    @include fullscreen;
    @include box;
    background: $c-mask-blue;
    z-index: 100;
    &__cnt {
        text-align: center;
        &__qrcode {
            width: 34vw;
            display: block;
            margin: 0 auto .2rem;
        }
        &__text {
            font-weight: bold;
            font-size: .16rem;
            color: $c-greenblue;
            p {
                margin: 0;
            }
        }
    }
}
</style>

