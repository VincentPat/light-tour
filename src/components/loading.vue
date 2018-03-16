<template>
    <div class="loading">
        <div class="loading__progress">
            <div class="loading__progress__bar"
                :style="progressWidth">
            </div>
        </div>
        <img src="https://static.cdn.24haowan.com/img/32/32152068793321119.png" class="loading__text">
    </div>
</template>

<script>
export default {
    name: 'loading',
    computed: {
        progressWidth() {
            return `width: ${this.progress}%`;
        }
    },
    data() {
        return {
            progress: 0
        };
    },
    mounted() {
        this.$bus.$on('progress', (progress) => {
            this.progress += progress;
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.loading {
    @include fullscreen;
    @include bg-cover;
    background-position: center top;
    background-image: url('https://static.cdn.24haowan.com/img/32/32152110787295334.png');
    z-index: 100;
    &__progress {
        @include transform-center;
        transform: translate(-50%, 6vh);
        width: 80vw;
        height: 10px;
        border-radius: 10px;
        overflow: hidden;
        background: $c-darkblue;
        &__bar {
            background: linear-gradient(90deg, $c-orange, $c-yellow);
            height: 100%;
            transition: all ease-out .2s;
            border-radius: 10px;
        }
    }
    &__text {
        position: absolute;
        top: 60vh;
        width: 60vw;
        left: 20vw;
    }
}
</style>
