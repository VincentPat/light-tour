<template>
    <div class="floor3">
        <div class="floor3__stage"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152069065072827.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152069064937101.png"
                class="after"
                :class="{ active }">
        </div>
        <img class="floor3__light"
            src="https://static.cdn.24haowan.com/img/32/32152069065112254.png">
        <div class="floor3__name"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152069065246564.png"
                class="before animated infinite pulse"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152075997649029.png"
                class="after"
                :class="{ active }">
        </div>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922159093.png"
                class="floor3__achieve"
                v-show="active">
        </transition>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/3215205692236479.png"
                class="floor3__guide wanderVer"
                v-show="!active">
        </transition>
    </div>
</template>

<script>
export default {
    name: 'floor3',
    data() {
        return {
            active: false
        };
    },
    methods: {
        showInfo() {
            if (!this.active) {
                this.$bus.$emit('showFloorDesc', 3);
            }
        },
        showLight() {
            const light = document.querySelectorAll('.floor3__light')[0];
            light.style.opacity = '1';
            setTimeout(() => {
                light.className += ' active';
                light.style.opacity = '0';
                setTimeout(() => {
                    light.className = light.className.replace(' active', '');
                }, 1000);
            }, 50);
        },
        startShowLight() {
            const timeout = 2000 + Math.random() * 2000;
            setTimeout(() => {
                this.showLight();
                this.startShowLight();
            }, timeout);
        }
    },
    mounted() {
        this.$bus.$on('goal', ({ no }) => {
            if (no === 3) {
                setTimeout(() => {
                    this.active = true;
                }, 2000);
            }
        });
        this.startShowLight();
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.floor3 {
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
    &__light {
        pointer-events: none;
        position: absolute;
        top: 25%;
        left: 44%;
        width: 70vw;
        opacity: 0;
        &.active {
            transition: all linear .8s;
        }
    }
    &__name {
        position: absolute;
        top: 63%;
        right: 13%;
        width: 28vw;
    }
    &__guide {
        position: absolute;
        top: 85%;
        right: 13%;
        width: 6vw;
    }
    &__achieve {
        position: absolute;
        top: 17%;
        left: 40%;
        width: 28vw;
    }
}
</style>
