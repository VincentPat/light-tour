<template>
    <div id="app">
        <transition name="fade" appear>
            <loading v-show="showLoading"></loading>
        </transition>
        <home v-show="showHome"></home>
        <transition name="fade">
            <share v-show="showShare"></share>
        </transition>
        <transition name="fade">
            <subscribe v-show="showSubscribe"></subscribe>
        </transition>
        <transition name="fade">
            <floor-desc v-show="showFloorDesc"
                :cn="floorDesc.cn"
                :en="floorDesc.en"
                :rule="floorDesc.rule"
                :address="floorDesc.address">
            </floor-desc>
        </transition>
        <transition name="fade">
            <goal v-show="showGoal"></goal>
        </transition>
        <transition name="fade">
            <scan-error v-show="showScanError"></scan-error>
        </transition>
        <transition name="fade">
            <complete v-show="showComplete"></complete>
        </transition>
        <transition name="fade">
            <get-prize v-show="showGetPrize"
                :prizeImg="prizeImg"
                :prizeName="prizeName">
            </get-prize>
        </transition>
    </div>
</template>

<script>
import loading from '@/components/loading';
import home from '@/components/home';
import share from '@/components/share';
import subscribe from '@/components/subscribe';
import floorDesc from '@/components/floor-desc';
import floorDescData from '@/floor-desc-data';
import goal from '@/components/goal';
import scanError from '@/components/scan-error';
import complete from '@/components/complete';
import getPrize from '@/components/get-prize';

export default {
    name: 'App',
    components: {
        loading,
        home,
        share,
        subscribe,
        floorDesc,
        goal,
        scanError,
        complete,
        getPrize
    },
    data() {
        return {
            audios: {},
            audiosSrc: {
                bg: 'https://static.cdn.24haowan.com/music/32/321520852919.mp3',
                click: 'https://static.cdn.24haowan.com/music/32/321520853009.mp3',
                goal: 'https://static.cdn.24haowan.com/music/32/321520853048.mp3',
                prize: 'https://static.cdn.24haowan.com/music/32/321520853075.mp3'
            },
            muted: false,
            showLoading: true,
            showHome: false,
            showShare: false,
            showSubscribe: false,
            showFloorDesc: false,
            floorDesc: {},
            floorDescData,
            currentFloor: 1,
            completedFloors: [],
            showGoal: false,
            showScanError: false,
            showComplete: false,
            showGetPrize: false,
            prizeName: '广州K11艺术展览九折优惠券一张',
            prizeImg: 'https://static.cdn.24haowan.com/img/32/32152076305274928.png'
        };
    },
    methods: {
        initEvents() {
            this.$bus.$on('ready', () => {
                this.showHome = true;
                setTimeout(() => {
                    this.showLoading = false;
                }, 500);
            });
            this.$bus.$on('progress', (progress) => {
                if (progress >= 100) {
                    this.$bus.$emit('ready');
                }
            });
            this.$bus.$on('switchMusic', () => {
                this.switchMusic();
            });
            // 分享页
            this.$bus.$on('showShare', () => {
                this.showShare = true;
            });
            this.$bus.$on('hideShare', () => {
                this.showShare = false;
            });
            // 关注公众号
            this.$bus.$on('showSubscribe', () => {
                this.showSubscribe = true;
            });
            this.$bus.$on('hideSubscribe', () => {
                this.showSubscribe = false;
            });
            // 楼层介绍
            this.$bus.$on('showFloorDesc', (no) => {
                this.playMusic('click');
                this.floorDesc = this.floorDescData[no - 1];
                this.currentFloor = no;
                this.showFloorDesc = true;
            });
            this.$bus.$on('hideFloorDesc', () => {
                this.showFloorDesc = false;
            });
            // 扫描
            this.$bus.$on('scan', () => {
                setTimeout(() => {
                    this.$bus.$emit('scanSuccess');
                }, 1000);
            });
            this.$bus.$on('scanSuccess', () => {
                this.$bus.$emit('hideFloorDesc');
                this.$bus.$emit('goal', this.currentFloor);
            });
            this.$bus.$on('scanError', () => {
                this.$bus.$emit('hideFloorDesc');
                this.$bus.$emit('showScanError');
            });
            // 扫描错误
            this.$bus.$on('showScanError', () => {
                this.showScanError = true;
            });
            this.$bus.$on('hideScanError', () => {
                this.showScanError = false;
            });
            // 点亮成功
            this.$bus.$on('goal', (no) => {
                if (this.completedFloors.indexOf(no) === -1) this.completedFloors.push(no);
                this.$bus.$emit('showGoal');
                this.playMusic('goal');
                if (this.completedFloors.length >= 6) { // 全部完成
                    setTimeout(() => {
                        this.$bus.$emit('complete');
                    }, 3500);
                }
            });
            this.$bus.$on('showGoal', () => {
                this.showGoal = true;
                setTimeout(() => {
                    this.$bus.$emit('hideGoal');
                }, 3000);
            });
            this.$bus.$on('hideGoal', () => {
                this.showGoal = false;
            });
            // 完全通关
            this.$bus.$on('complete', () => {
                this.$bus.$emit('showComplete');
            });
            // 完成所有任务
            this.$bus.$on('showComplete', () => {
                this.playMusic('prize');
                this.showComplete = true;
            });
            this.$bus.$on('hideComplete', () => {
                this.showComplete = false;
            });
            // 获得奖品
            this.$bus.$on('getCompletePrize', () => {
                this.$bus.$emit('hideComplete');
                this.$bus.$emit('showGetPrize');
            });
            this.$bus.$on('showGetPrize', () => {
                this.showGetPrize = true;
            });
            this.$bus.$on('hideGetPrize', () => {
                this.showGetPrize = false;
            });
        },
        load() {
            setTimeout(() => {
                this.$bus.$emit('progress', 100);
            }, 100);
        },
        initAudios() {
            Object.keys(this.audiosSrc).forEach((key) => {
                const url = this.audiosSrc[key];
                this.audios[key] = new Audio();
                this.audios[key].src = url;
                if (key === 'bg') this.audios[key].loop = true;
            });
        },
        playMusic(key) {
            if (key in this.audios && !this.muted) this.audios[key].play();
        },
        pauseMusic() {
            this.audios.bg.pause();
        },
        switchMusic() {
            this.muted = !this.muted;
            if (!this.muted) { // 重新播放音乐
                this.playMusic('bg');
            } else { // 暂停播放音乐
                this.pauseMusic();
            }
        }
    },
    mounted() {
        this.initAudios();
        this.initEvents();
        this.load();
        window.bus = this.$bus;
        window.app = this;
    }
};
</script>

<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
}
</style>
