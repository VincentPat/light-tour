<template>
    <div class="floor2">
        <div class="floor2__stage"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152068873887070.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152068873890555.png"
                class="after"
                :class="{ active }">
        </div>
        <div class="floor2__float">
            <img src="https://static.cdn.24haowan.com/img/32/32152068873787062.png"
                class="floor2__float__umbrella floating"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152068873959531.png"
                class="floor2__float__chair1 floating"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152068874051182.png"
                class="floor2__float__chair2 floating"
                :class="{ active }">
        </div>
        <div class="floor2__name"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152068874137571.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152075997634510.png"
                class="after"
                :class="{ active }">
        </div>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922596263.png"
                class="floor2__achieve"
                v-show="active">
        </transition>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/3215205692236479.png"
                class="floor2__guide wanderVer"
                v-show="!active">
        </transition>
    </div>
</template>

<script>
export default {
    name: 'floor2',
    data() {
        return {
            active: false
        };
    },
    methods: {
        showInfo() {
            this.active = true;
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.floor2 {
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
    &__float {
        img {
            transition: all linear .5s;
            &.active {
                opacity: 0.5;
            }
        }
        &__umbrella {
            position: absolute;
            top: 16%;
            left: 21%;
            width: 18vw;
            animation-delay: .2s;
        }
        &__chair1 {
            position: absolute;
            top: 30%;
            left: 28%;
            width: 18vw;
            animation-delay: 1.4s;
        }
        &__chair2 {
            position: absolute;
            top: 16%;
            left: 68%;
            width: 14vw;
            animation-delay: .7s;
        }
    }
    &__name {
        position: absolute;
        top: 58%;
        left: 24%;
        width: 25vw;
    }
    &__achieve {
        position: absolute;
        top: 6%;
        left: 38%;
        width: 28vw;
    }
    &__guide {
        position: absolute;
        top: 79%;
        left: 25%;
        width: 6vw;
    }
}

.floating {
    animation-name: floating;
    animation-duration: 2s;
    animation-iteration-count: infinite;
    animation-direction: alternate;
    animation-timing-function: ease-out;
}

@keyframes floating {
    from {
        transform: translate(0, -20%);
    }
    to {
        transform: translate(0, 0);
    }
}
</style>
