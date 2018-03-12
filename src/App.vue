<template>
    <div id="app" @touchmove.stop>
        <transition name="fade" appear>
            <loading v-show="showLoading"></loading>
        </transition>
        <home :class="{ show: showHome }"></home>
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
import wx from '@/plugin/wx';
import axios from 'axios';
import { Base64 } from 'js-base64';

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
            progress: 0,
            timestamp: Date.now(),
            envData: {},
            imgs: [],
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
        // 初始化环境数据
        initEnvData() {
            this.env = location.host.match(/api.24haowan.com/ig) ? 'production' : 'test';
            if (this.env === 'test') {
                this.envData = {
                    host: 'http://test.api.klub11.com',
                    account_id: '6',
                    apiKey: '271151990342466',
                    apiSecret: 'rAoV68K4',
                    interfaceId: 'fb9cff6e93ff6342f066',
                    skey: '21da1090',
                };
            } else if (this.env === 'production') {
                this.envData = {
                    host: 'https://api.klub11.com',
                    account_id: '6',
                    apiKey: '271151990342466',
                    apiSecret: 'rAoV68K4',
                    interfaceId: 'fb9cff6e93ff6342f066',
                    skey: '21da1090',
                };
            }
        },
        // 事件
        initEvents() {
            this.$bus.$on('ready', () => {
                this.showHome = true;
                setTimeout(() => {
                    this.showLoading = false;
                }, 500);
            });
            this.$bus.$on('progress', (progress) => {
                this.progress += progress;
                if (this.progress >= 100) {
                    let countdown = 3000 - (Date.now() - this.timestamp);
                    countdown = countdown > 0 ? countdown : 1;
                    setTimeout(() => {
                        this.$bus.$emit('ready');
                    }, countdown);
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
            // 禁止滑动
            document.body.addEventListener('touchmove', (e) => {
                e.preventDefault();
            });
        },
        // 加载
        load() {
            const imgs = document.querySelectorAll('img');
            imgs.forEach((img) => {
                const tmp = new Image();
                tmp.onload = () => {
                    this.$bus.$emit('progress', 1 / imgs.length * 80);
                };
                tmp.src = img.src;
            });
        },
        // 音频相关
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
        },
        // 请求相关
        getWxConfig() {
            const url = `${this.envData.host}/v1/wechat-js/js-config`;
            const timestamp = Date.now();
            axios({
                method: 'get',
                url,
                params: {
                    apiKey: this.envData.apiKey,
                    interfaceId: this.envData.interfaceId,
                    timestamp,
                    sign: 'ABC',
                    account_id: this.envData.account_id,
                    domain_url: location.href.split('#')[0]
                },
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                }
            }).then((response) => {
                const result = this.decodeReturnData(response.data);
                console.log(response);
                console.log(result);
                const config = result.data;
                wx.configWx(config);
            }).catch((error) => {
                console.error(error);
            });
        },
        processRequestData(url, data) {
            const dataArr = [];
            Object.keys(data).forEach((key) => {
                dataArr.push(`${key}=${data[key]}`);
            });
            return `${url}?${dataArr.join('&')}`;
        },
        decodeReturnData(str) {
            let result = str;
            const skey = this.envData.skey;
            const replaceStr = {
                mqtp: ':',
                mbscd: '=',
                nnddt: '+',
                abcde: '/',
                adted: '|'
            };
            Object.keys(replaceStr).forEach((char) => {
                result = result.replace(new RegExp(char, 'g'), replaceStr[char]);
            });
            const len = result.length;
            const charArr = [];
            const charNum = Math.floor(len / 2);
            for (let i = 1; i <= charNum; i += 1) {
                const startIndex = (i - 1) * 2;
                const char = result.substr(startIndex, 2);
                charArr.push(char);
            }
            const skeyArr = skey.split('');
            const skeyArrLen = skeyArr.length;
            for (let key = 0; key < skeyArrLen; key += 1) {
                const value = skeyArr[key];
                if (key <= skeyArrLen && charArr[key][1] === value) {
                    charArr[key] = charArr[key][0];
                }
            }
            const temp = charArr.join('');
            let temp2 = Base64.decode(temp);
            try {
                temp2 = JSON.parse(temp2);
            } catch (error) {
                console.error(error);
            }
            return temp2;
        }
    },
    mounted() {
        this.initEnvData();
        this.initAudios();
        this.initEvents();
        this.load();
        // this.getWxConfig();
        window.bus = this.$bus;
        window.app = this;

        console.log(this.decodeReturnData('e2y1Jdjab120R9l0IjowLCJtc2ciOiLmiJDlip8iLCJkYXRhIjp7ImRlYnVnIjpmYWxzZSwiYXBwSWQiOiJ3eDBmNWRjYTgwYTJmZTRlNGEiLCJ0aW1lc3RhbXAiOjE1MjA4NTk1NDIsIm5vbmNlU3RyIjoiNWFhNjc5OTYwMDViMyIsInNpZ25hdHVyZSI6ImI2OGY2M2IwNDIwYWNlMjY2MWIxMTYzODYxNTQ2Yzc5MWM4ZjU5MDEiLCJqc0FwaUxpc3QiOlsiaGlkZU9wdGlvbk1lbnUiLCJzaG93T3B0aW9uTWVudSIsImNsb3NlV2luZG93IiwiYWRkQ2FyZCIsImNob29zZUNhcmQiLCJvcGVuQ2FyZCJdfX0mbscd'));
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
