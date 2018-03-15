<template>
    <div class="share" @click="hideShare">
        <img src="https://static.cdn.24haowan.com/img/32/3215210874493342.png"
            class="share__img">
    </div>
</template>

<script>
export default {
    name: 'share',
    methods: {
        hideShare() {
            this.$bus.$emit('hideShare');
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';

.share {
    @include fullscreen;
    background: rgba(0, 0, 0, 0.5);
    z-index: 100;
    &__img {
        position: absolute;
        top: 0;
        right: 0;
        width: 44vw;
    }
}
</style>

