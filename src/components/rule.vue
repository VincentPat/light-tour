<template>
    <div class="rule">
        <div class="rule__desc">
            <div class="rule__desc--1">
                <p>光给世界带来了绚烂和狂欢</p>
                <p>正如K11给这座城市带来的变革与突破</p>
                <p>一种新的能量正逐步释放</p>
                <p>进入K11 开启Light Tour</p>
                <p>捕捉光的元素，寻找光的答案</p>
                <p>Where are you going tonight?</p>
                <p>To where the light is!</p>
            </div>
            <img src="https://static.cdn.24haowan.com/img/32/32152051869666093.png"
                class="rule__desc__splitor">
            <div class="rule__desc--2">
                <p>关注广州K11微信公众号</p>
                <p>跟随线上地图寻找六大任务点</p>
                <p>根据指令完成五站任务</p>
                <p>点亮地图，体验光的奥秘</p>
                <p>体验时间：3月31日-4月8日</p>
            </div>
            <img src="https://static.cdn.24haowan.com/img/32/32152051869890486.png"
                class="rule__desc__splitor">
            <div class="rule__desc--3">
                <p>完成任务五站任务，点亮地图</p>
                <p>可获展览9折优惠券1张</p>
                <p>每完成一站任务即获10互动积分</p>
                <p>分享好友可获50互动积分（每天限领两次）</p>
            </div>
        </div>
        <img src="https://static.cdn.24haowan.com/img/32/32152051868785613.png"
            class="rule__slider wanderVer">
    </div>
</template>

<script>
export default {
    name: 'rule'
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.rule {
    @include fullscreen;
    @include bg-cover;
    @include box;
    background-image: url('https://static.cdn.24haowan.com/img/32/32152051868387686.png');
    &__slider {
        position: absolute;
        bottom: 5vh;
        left: 46vw;
        width: 8vw;
    }
    &__desc {
        font-size: .14rem;
        color: $c-lightyellow;
        p {
            margin: 0px;
            line-height: .22rem;
        }
        &--1 {
            font-size: .12rem;
            p {
                line-height: .2rem;
            }
        }
        &__splitor {
            height: .3rem;
            display: block;
            margin: .2rem auto .1rem;
        }
    }
}
</style>

