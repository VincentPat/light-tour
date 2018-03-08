<template>
    <div class="loading">
        <div class="loading__progress">
            <div class="loading__progress__bar"
                :style="progressWidth">
            </div>
        </div>
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
            this.progress = progress;
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
    background-position: center bottom;
    background-image: url('https://static.cdn.24haowan.com/img/32/3215205186802582.png');
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
            background: linear-gradient(90deg, $c-orange, $c-yellow);;
            height: 100%;
            transition: all ease-out .2s;
            border-radius: 10px;
        }
    }
}
</style>
