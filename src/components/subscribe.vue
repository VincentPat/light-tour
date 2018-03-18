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
    data() {
        return {
            callback: null
        };
    },
    methods: {
        hideSubscribe() {
            this.$bus.$emit('hideSubscribe');
            setTimeout(() => {
                if (typeof this.callback === 'function') {
                    this.callback();
                    this.callback = null;
                }
            }, 400);
        }
    },
    mounted() {
        this.$bus.$on('showSubscribe', (callback) => {
            this.callback = callback;
        });
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
        font-family: 'CNB';
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
                margin-bottom: .06rem;
            }
        }
    }
}
</style>

