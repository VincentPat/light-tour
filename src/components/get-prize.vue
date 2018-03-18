<template>
    <div class="get-prize">
        <div class="get-prize__cnt">
            <div class="get-prize__cnt__text">
                <div class="get-prize__cnt__text__title">成功领取</div>
                <img :src="prizeImg" class="get-prize__cnt__text__img">
                <div class="get-prize__cnt__text__name" v-html="prizeName"></div>
            </div>
            <div class="get-prize__cnt__remind" v-html="remind"></div>
            <img src="https://static.cdn.24haowan.com/img/32/32152083519720909.png"
                class="get-prize__cnt__confirm"
                @click="hideGetPrize">
        </div>
    </div>
</template>

<script>
export default {
    name: 'get-prize',
    props: {
        prizeName: {
            default: null,
            type: String
        },
        prizeImg: {
            default: null,
            type: String
        }
    },
    computed: {
        remind() {
            if (!this.prizeName) return null;
            return `恭喜你！已成功领取${this.prizeName}，奖品已通过后台发放到微信卡券。进入微信卡券即可查看。`;
        }
    },
    methods: {
        hideGetPrize() {
            this.$bus.$emit('hideGetPrize');
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.get-prize {
    @include fullscreen;
    @include box;
    z-index: 100;
    background: $c-mask-blue;
    &__cnt {
        @include bg-contain;
        @include box;
        transform: translate(0, -50%);
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
            &__title {
                border-bottom: .02rem dashed $c-greenblue;
                padding-bottom: .06rem;
            }
            &__img {
                background: rgba(0, 0, 0, 0.5);
                width: 80%;
                display: block;
                margin: .12rem auto;
            }
            &__name {
                font-size: .14rem;
                margin-bottom: .06rem;
            }
        }
        &__remind {
            position: absolute;
            top: 100%;
            left: 50%;
            transform: translate(-50%, .2rem);
            color: $c-greenblue;
            font-size: .16rem;
            line-height: .24rem;
            font-weight: bold;
            width: 70vw;
            &:before {
                @include bg-contain;
                content: '';
                background-image: url('https://static.cdn.24haowan.com/img/32/32152077606226860.png');
                position: absolute;
                display: block;
                top: 0;
                right: 0;
                transform: translate(50%, -50%) rotate(90deg);
                width: .08rem;
                height: .08rem;
            }
            &:after {
                @include bg-contain;
                content: '';
                background-image: url('https://static.cdn.24haowan.com/img/32/32152077606226860.png');
                position: absolute;
                display: block;
                bottom: 0;
                left: 0;
                transform: translate(-170%, 50%) rotate(-90deg);
                width: .08rem;
                height: .08rem;
            }
        }
        &__confirm {
            position: absolute;
            top: 100%;
            left: 50%;
            width: 40vw;
            transform: translate(-50%, 1.4rem);
        }
    }
}
</style>
