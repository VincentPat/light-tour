<template>
    <div class="point">
        <div class="point__cnt">
            <img src="https://static.cdn.24haowan.com/img/32/32152083519747602.png"
                class="point__cnt__img">
            <div class="point__cnt__text">
                <div v-html="reason"></div>
                <div v-html="point"></div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'point',
    data() {
        return {
            reason: '成功点亮关卡',
            point: '获得10互动积分'
        };
    },
    mounted() {
        this.$bus.$on('showPoint', (type) => {
            if (type === 'share') { // 分享
                this.reason = '分享成功';
                this.point = '获得50互动积分';
            } else {
                this.reason = '成功点亮关卡';
                this.point = '获得10互动积分';
            }
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.point {
    @include fullscreen;
    @include box;
    z-index: 100;
    &__cnt {
        border-radius: .2rem;
        border: .04rem solid $c-greenblue;
        background: $c-mask-blue;
        padding: .2rem 0 .2rem;
        transform: translate(0, -50%);
        color: $c-greenblue;
        width: 60vw;
        &__img {
            width: 8vw;
            display: block;
            margin: 0 auto .1rem;
        }
        &__text {
            font-size: .2rem;
            font-weight: bold;
            line-height: .26rem;
        }
    }
}
</style>

