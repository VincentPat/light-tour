<template>
    <div class="goal">
        <div class="goal__cnt">
            <img src="https://static.cdn.24haowan.com/img/32/32152083519747602.png"
                class="goal__cnt__img">
            <div class="goal__cnt__text">
                <div>成功点亮关卡</div>
                <div>获得50互动积分</div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'goal'
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.goal {
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

