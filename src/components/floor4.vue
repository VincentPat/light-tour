<template>
    <div class="floor4">
        <div class="floor4__stage"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/3215207602236898.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152076022224996.png"
                class="after"
                :class="{ active }">
        </div>
        <div class="floor4__name"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152076022310078.png"
                class="before animated infinite pulse"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/321520759977242.png"
                class="after"
                :class="{ active }">
        </div>
        <transition name="fade">
            <img class="floor4__animal bling"
                src="https://static.cdn.24haowan.com/img/32/32152076022334105.png">
        </transition>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922159093.png"
                class="floor4__achieve"
                v-show="active">
        </transition>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/3215205692236479.png"
                class="floor4__guide wanderVer"
                v-show="!active">
        </transition>
    </div>
</template>

<script>
export default {
    name: 'floor4',
    data() {
        return {
            active: false
        };
    },
    methods: {
        showInfo() {
            if (!this.active) {
                this.$bus.$emit('showFloorDesc', 4);
            }
        }
    },
    mounted() {
        this.$bus.$on('goal', ({ no }) => {
            if (no === 4) this.active = true;
        });
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.floor4 {
    @include fullfill;
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
        top: 64%;
        right: 20%;
        width: 25vw;
    }
    &__animal {
        position: absolute;
        top: 14%;
        left: 28%;
        width: 46vw;
        animation-duration: 1s;
    }
    &__guide {
        position: absolute;
        top: 85%;
        right: 20%;
        width: 6vw;
    }
    &__achieve {
        position: absolute;
        top: 6%;
        left: 38%;
        width: 28vw;
    }
}
</style>
