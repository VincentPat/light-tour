<template>
    <div class="floor6">
        <div class="floor6__stage"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152076249178391.png"
                class="before"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152076249098250.png"
                class="after"
                :class="{ active }">
        </div>
        <div class="floor6__name"
            @click="showInfo">
            <img src="https://static.cdn.24haowan.com/img/32/32152076248360830.png"
                class="before animated infinite pulse"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152075997811605.png"
                class="after"
                :class="{ active }">
        </div>
        <div class="floor6__note">
            <img src="https://static.cdn.24haowan.com/img/32/32152076249130446.png"
                class="floor6__note--1 floating"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152076249226665.png"
                class="floor6__note--2 floating"
                :class="{ active }">
            <img src="https://static.cdn.24haowan.com/img/32/32152076248294946.png"
                class="floor6__note--3 floating"
                :class="{ active }">
        </div>
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/32152056922159093.png"
                class="floor6__achieve"
                v-show="active">
        </transition>
        <img src="https://static.cdn.24haowan.com/img/32/32152076305274928.png"
            class="floor6__share"
            @click="showShare">
        <transition name="fade">
            <img src="https://static.cdn.24haowan.com/img/32/3215205692236479.png"
                class="floor6__guide wanderVer"
                v-show="!active">
        </transition>
    </div>
</template>

<script>
export default {
    name: 'floor6',
    data() {
        return {
            timeout: false,
            active: false,
            noteCnt: null,
            noteImgs: [
                'https://static.cdn.24haowan.com/img/32/32152076249214264.png',
                'https://static.cdn.24haowan.com/img/32/32152076249125161.png',
                'https://static.cdn.24haowan.com/img/32/32152076248285930.png',
                'https://static.cdn.24haowan.com/img/32/3215207624819885.png',
                'https://static.cdn.24haowan.com/img/32/32152076248120360.png',
                'https://static.cdn.24haowan.com/img/32/32152076248370404.png'
            ]
        };
    },
    methods: {
        showInfo() {
            if (!this.active) {
                this.$bus.$emit('showFloorDesc', 6);
            }
        },
        showShare() {
            this.$bus.$emit('showShare');
        },
        showNote() {
            this.timeout = setTimeout(() => {
                this.createNote();
                this.showNote();
            }, 300);
        },
        createNote() {
            const direction = Math.random() > 0.5 ? 1 : -1;
            const delta = 100 + Math.random() * 100;
            let note = document.createElement('img');
            const rnd = Math.floor(Math.random() * this.noteImgs.length);
            note.src = this.noteImgs[rnd];
            note.className = 'floor6__note__float';
            note.name = 'first';
            note.addEventListener('webkitTransitionEnd', () => {
                if (note) {
                    if (note.name === 'first') { // 执行完第一个动画
                        note.name = 'second';
                        note.style.transition = 'all linear 1s';
                        note.style.transform = `translate(${direction * delta}%, ${-20000 / delta * 2}%) scale(1, 1)`;
                        note.style.opacity = '0';
                    } else if (note.name === 'second') {
                        this.noteCnt.removeChild(note);
                        note = null;
                    }
                }
            });
            this.noteCnt.appendChild(note);
            setTimeout(() => {
                note.style.transform = `translate(${direction * (delta - 100) / 2}%, -50%) scale(1, 1)`;
            }, 100);
        }
    },
    mounted() {
        this.noteCnt = document.querySelectorAll('.floor6__note')[0];
        this.$bus.$on('showFloors', () => {
            if (!this.timeout) this.showNote();
        });
        this.$bus.$on('goal', ({ no }) => {
            if (no === 6) {
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

.floor6 {
    @include fullfill;
    overflow: hidden;
    padding-bottom: .2rem;
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
        top: 58%;
        right: 20%;
        width: 25vw;
    }
    &__note {
        pointer-events: none;
        img {
            transition: all linear .5s;
            &.active {
                opacity: 0.5;
            }
        }
        &--1 {
            position: absolute;
            top: 12%;
            left: 23%;
            width: 7vw;
            animation-delay: .2s;
        }
        &--2 {
            position: absolute;
            top: 20%;
            left: 60%;
            width: 7vw;
            animation-delay: 1.4s;
        }
        &--3 {
            position: absolute;
            top: 35%;
            left: 72%;
            width: 7vw;
            animation-delay: .7s;
        }
        &__float {
            position: absolute;
            width: 7vw;
            top: 20%;
            left: 50%;
            transform: translate(0, 0) scale(.01, .01);
        }
    }
    &__guide {
        position: absolute;
        top: 80%;
        right: 20%;
        width: 6vw;
    }
    &__achieve {
        position: absolute;
        top: 0;
        left: 16%;
        width: 28vw;
    }
    &__share {
        position: absolute;
        width: 50vw;
        bottom: 0;
        left: 25vw;
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
