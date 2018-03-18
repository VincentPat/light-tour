<template>
    <div class="floor-desc" @touchmove.stop.prevent>
        <div class="floor-desc__cnt">
            <div class="floor-desc__cnt__desc">
                <img src="https://static.cdn.24haowan.com/img/32/32152077606189344.png"
                    class="floor-desc__cnt__desc__close"
                    @click="hideFloorDesc">
                <div class="floor-desc__cnt__desc__name">
                    <h3 v-html="cn"></h3>
                    <h4 v-html="en"></h4>
                </div>
                <div class="floor-desc__cnt__desc__rule">
                    <h4>活动规则：</h4>
                    <ul>
                        <li v-for="(item, index) in rule"
                            :key="index"
                            v-html="item">
                        </li>
                    </ul>
                </div>
                <div class="floor-desc__cnt__desc__address">
                    <h4>
                        <span>活动地点：</span>
                        <span v-html="address"></span>
                    </h4>
                </div>
            </div>
            <div class="floor-desc__cnt__remind">
                <div>现场完成任务后</div>
                <br>
                <div>请找任务点工作人员扫码认证</div>
            </div>
            <img src="https://static.cdn.24haowan.com/img/32/32152077606239212.png"
                class="floor-desc__cnt__scan"
                @click="scan">
        </div>
    </div>
</template>

<script>
export default {
    name: 'floor-desc',
    props: {
        cn: {
            default: null,
            type: String
        },
        en: {
            default: null,
            type: String
        },
        rule: {
            default: () => [],
            type: Array
        },
        address: {
            default: null,
            type: String
        }
    },
    methods: {
        hideFloorDesc() {
            this.$bus.$emit('hideFloorDesc');
        },
        scan() {
            this.$bus.$emit('scan');
        }
    }
};
</script>

<style lang="scss">
@import '../style/mixin.scss';
@import '../style/color.scss';

.floor-desc {
    @include fullscreen;
    @include box;
    z-index: 200;
    background: $c-mask-blue;
    &__cnt {
        width: 90vw;
        color: $c-greenblue;
        &__desc {
            @include bg-contain;
            position: relative;
            min-height: 30vh;
            background-size: 100% 100%;
            background-image: url('https://static.cdn.24haowan.com/img/32/32152077606093443.png');
            width: 100%;
            text-align: left;
            padding: 10vw;
            box-sizing: border-box;
            font-size: .2rem;
            font-family: 'CNB';
            &__close {
                position: absolute;
                top: 10vw;
                right: 6vw;
                width: 5vw;
                z-index: 1;
            }
            &__name {
                position: relative;
                padding-left: 10vw;
                &:before {
                    @include bg-contain;
                    background-image: url('https://static.cdn.24haowan.com/img/32/32152077606183517.png');
                    content: '';
                    width: 8vw;
                    height: 100%;
                    position: absolute;
                    top: 0;
                    left: 0;
                    display: block;
                }
                h3 {
                    font-size: .2rem;
                    margin: 0;
                }
                h4 {
                    font-family: 'ENB';
                    margin: 0;
                    font-size: .16rem;
                }
            }
            &__rule {
                h4 {
                    font-size: .16rem;
                    margin: .1rem 0;
                    font-weight: bold;
                }
                ul {
                    font-family: 'CN';
                    margin: 0;
                    font-size: .14rem;
                    padding-left: .24rem;
                }
            }
            &__address {
                h4 {
                    font-size: .16rem;
                    margin: .1rem 0;
                    font-weight: bold;
                }
            }
        }
        &__remind {
            font-size: .16rem;
            font-weight: bold;
            margin: .2rem 0;
            font-family: 'CNB';
            div {
                display: inline-block;
                position: relative;
                &:first-child {
                    margin-bottom: .06rem;
                    &:before {
                        @include bg-contain;
                        content: '';
                        background-image: url('https://static.cdn.24haowan.com/img/32/32152077606226860.png');
                        position: absolute;
                        display: block;
                        top: 0;
                        left: 0;
                        transform: translate(-120%, -20%);
                        width: .08rem;
                        height: .08rem;
                    }
                }
                &:last-child {
                    &:after {
                        @include bg-contain;
                        content: '';
                        background-image: url('https://static.cdn.24haowan.com/img/32/32152077606226860.png');
                        position: absolute;
                        display: block;
                        bottom: 0;
                        right: 0;
                        transform: translate(120%, 20%) rotate(180deg);
                        width: .08rem;
                        height: .08rem;
                    }
                }
            }
        }
        &__scan {
            width: 40vw;
            display: block;
            margin: 0 auto;
        }
    }
}
</style>
