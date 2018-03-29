<template>
    <div class="floor5">
        <div class="floor5__stage"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152076144441534.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152076144456808.png"
                class="after"
                :class="{ active }">
        </div>
        <div class="floor5__name"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152076144546387.png"
                class="before animated infinite pulse"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152075997779284.png"
                class="after"
                :class="{ active }">
        </div>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922596263.png"
                class="floor5__achieve"
                v-show="active">
        </transition>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/3215205692236479.png"
                class="floor5__guide wanderVer"
                v-show="!active">
        </transition>
    </div>
</template>

<script>
export default {
    name: 'floor5',
    data() {
        return {
            active: false
        };
    },
    methods: {
        showInfo() {
            if (!this.active) {
                this.$bus.$emit('showFloorDesc', 5);
            }
        }
    },
    mounted() {
        this.$bus.$on('goal', ({ no }) => {
            if (no === 5) {
                setTimeout(() => {
                    this.active = true;
                }, 2000);
            }
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.floor5 {
    @include fullfill;
    overflow: hidden;
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
        top: 54%;
        left: 14%;
        width: 25vw;
    }
    &__guide {
        position: absolute;
        top: 75%;
        left: 14%;
        width: 6vw;
    }
    &__achieve {
        position: absolute;
        top: 6%;
        left: 37%;
        width: 28vw;
    }
}
</style>
