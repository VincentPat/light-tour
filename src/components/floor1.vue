<template>
    <div class="floor1">
        <div class="floor1__stage"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922035508.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152056921951818.png"
                class="after"
                :class="{ active }">
        </div>
        <div class="floor1__name"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922228587.png"
                class="before animated infinite pulse"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152075997593402.png"
                class="after"
                :class="{ active }">
        </div>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922159093.png"
                class="floor1__achieve"
                v-show="active">
        </transition>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/3215205692236479.png"
                class="floor1__guide wanderVer"
                v-show="!active">
        </transition>
    </div>
</template>

<script>
export default {
    name: 'floor1',
    data() {
        return {
            active: false
        };
    },
    methods: {
        showInfo() {
            if (!this.active) {
                this.$bus.$emit('showFloorDesc', 1);
            }
        }
    },
    mounted() {
        this.$bus.$on('goal', ({ no }) => {
            if (no === 1) this.active = true;
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.floor1 {
    @include fullfill;
    top: 0;
    img {
        &.before, &.after {
            width: 100%;
            transition: all linear .5s;
        }
        &.before {
            &.active {
                opacity: 0;
            }
        }
        &.after {
            position: absolute;
            top: 0;
            left: 0;
            opacity: 0;
            &.active {
                opacity: 1;
            }
        }
    }
    &__stage {
        position: absolute;
        top: 0;
        right: 0;
        width: 100vw;
    }
    &__name {
        position: absolute;
        top: 46%;
        right: 10%;
        width: 25vw;
    }
    &__achieve {
        position: absolute;
        top: 0;
        left: 22%;
        width: 28vw;
    }
    &__guide {
        position: absolute;
        top: 66%;
        right: 10%;
        width: 6vw;
    }
}
</style>
