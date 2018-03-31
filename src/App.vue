<template>
    <div id="app" @touchmove.stop.prevent>
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
            <point v-show="showPoint"></point>
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
import point from '@/components/point';
import scanError from '@/components/scan-error';
import complete from '@/components/complete';
import getPrize from '@/components/get-prize';
import wx from '@/plugin/wx';
import axios from 'axios';
import md5 from 'md5';

export default {
    name: 'App',
    components: {
        loading,
        home,
        share,
        subscribe,
        floorDesc,
        point,
        scanError,
        complete,
        getPrize
    },
    data() {
        return {
            hasGotPrize: false, // 是否已经获得奖品
            isMember: false, // 是否为会员
            progress: 0, // 加载进度
            timestamp: Date.now(), // 进入页面的时间
            envData: {}, // 运行环境参数
            imgs: [], // 图片
            audios: {}, // 音频
            audiosSrc: {
                bg: 'https://24haowan-cdn.shanyougame.com/dingzhi/k11-light-tour/bgm.mp3',
                click: 'https://static.cdn.24haowan.com/music/32/321520853009.mp3',
                goal: 'https://static.cdn.24haowan.com/music/32/321520853048.mp3',
                prize: 'https://static.cdn.24haowan.com/music/32/321520853075.mp3'
            }, // 音频地址
            muted: false, // 是否静音
            currentFloor: 1, // 当前展示楼层
            completedFloors: [], // 已完成楼层
            floorDesc: {}, // 楼层介绍
            floorDescData, // 楼层介绍数据
            // 奖品名称
            prizeName: '广州K11艺术展览九折优惠券一张',
            prizeImg: 'https://static.cdn.24haowan.com/img/32/32152100787513264.jpg',
            // 模态框
            showLoading: true,
            showHome: false,
            showShare: false,
            showSubscribe: false,
            showFloorDesc: false,
            showPoint: false,
            showScanError: false,
            showComplete: false,
            showGetPrize: false,
            musicPreplayed: false,
            testCountNum: 0,
        };
    },
    methods: {
        // 初始化环境数据
        initEnvData() {
            // this.env = location.host.match(/api.24haowan.com/ig) ? 'production' : 'test';
            this.env = 'production';
            if (this.env === 'test') {
                this.envData = {
                    cardId: 'pC2Vgv8HOaMpzoY8v9MH9TTi3AOc',
                    host24: 'http://test.ac.24haowan.com'
                };
            } else if (this.env === 'production') {
                this.envData = {
                    cardId: 'pQzhSw4PZNIiE1WenpDpZk4bs_K8',
                    host24: 'http://lighttour-v.klub11.com'
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
                this.testCount();
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
                window.wx.scanQRCode({
                    needResult: 1, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
                    scanType: ['qrCode', 'barCode'], // 可以指定扫二维码还是一维码，默认二者都有
                    success: (res) => {
                        const result = res.resultStr; // 当needResult 为 1 时，扫码返回的结果
                        if (md5(`k11-${this.currentFloor}`) === result) { // 扫描楼层对了
                            this.$bus.$emit('scanSuccess', result);
                        } else {
                            this.$bus.$emit('scanError');
                        }
                    }
                });
            });
            this.$bus.$on('scanSuccess', (str) => {
                axios({
                    method: 'get',
                    url: `${this.envData.host24}/member/markLocation`,
                    params: {
                        str
                    }
                }).then((response) => {
                    this.$bus.$emit('hideFloorDesc');
                    const result = response.data;
                    console.log(result);
                    if (result.code === 0) {
                        this.$bus.$emit('goal', {
                            no: this.currentFloor
                        });
                    }
                }).catch((error) => {
                    console.error(error);
                    this.$bus.$emit('hideFloorDesc');
                });
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
            this.$bus.$on('goal', ({ no, initial }) => {
                if (this.completedFloors.indexOf(no) === -1) {
                    this.completedFloors.push(no);
                    if (!initial) {
                        this.playMusic('goal');
                        this.$bus.$emit('showPoint', 'goal');
                    }
                }
                if (this.completedFloors.length >= 5
                    && !this.hasGotPrize
                    && !initial) { // 达成条件
                    setTimeout(() => {
                        this.$bus.$emit('complete');
                    }, 3500);
                }
            });
            this.$bus.$on('showPoint', () => {
                this.showPoint = true;
                setTimeout(() => {
                    this.$bus.$emit('hidePoint');
                }, 2000);
            });
            this.$bus.$on('hidePoint', () => {
                this.showPoint = false;
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
                /* eslint no-underscore-dangle: "off" */
                window._vio.push(['_trackEvent', 'gift']);
                window._hmt.push(['_trackEvent', 'button', 'click', 'GetPrize']);
                this.addCard(() => {
                    this.hasGotPrize = true;
                    this.$bus.$emit('hideComplete');
                    this.$bus.$emit('showGetPrize');
                });
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
            // 自动播放音乐
            document.addEventListener('WeixinJSBridgeReady', () => {
                this.playMusic('bg');
            });
            // 自动播放音乐
            document.body.addEventListener('touchstart', () => {
                this.playMusic('bg');
                if (!this.musicPreplayed) this.preplayMusic();
            });
            // 分享回调
            this.$bus.$on('shareCallback', () => {
                window._vio.push(['_trackEvent', 'share']);
                window._hmt.push(['_trackEvent', 'share', 'share', 'share']);
                this.$bus.$emit('hideShare');
                axios({
                    method: 'get',
                    url: `${this.envData.host24}/member/share`
                }).then((response) => {
                    const result = response.data;
                    console.log(result);
                    if (result.code === 0) {
                        let callback = null;
                        if (result.data.sendPoint === 'yes') { // 已发放积分，每天限定两次
                            callback = () => {
                                this.$bus.$emit('showPoint', 'share');
                            };
                        }
                        if (result.data.isSubscribe === 0) { // 未关注公众号
                            this.$bus.$emit('showSubscribe', callback);
                        } else if (typeof callback === 'function') {
                            callback();
                        }
                    }
                });
            });
            // 滑动页面
            this.$bus.$on('slideChange', ({ activeIndex, callback }) => {
                if (activeIndex >= 2 && !this.isMember) {
                    location.href = `https://app.klub11.com/?r=page/auth&account_id=10&origin=264&css=2&_redirecturl=${encodeURIComponent(location.href.split('#')[0])}`;
                }
                if (activeIndex === 2 && this.isMember) {
                    callback();
                }
            });
            // 页面加载完毕
            window.onload = () => {
                this.$bus.$emit('progress', 20);
            };
        },
        // 加载
        load() {
            const imgs = document.querySelectorAll('img');
            imgs.forEach((img) => {
                const tmp = new Image();
                tmp.onload = () => {
                    this.$bus.$emit('progress', 1 / imgs.length * 60);
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
        // 预播放音乐
        preplayMusic() {
            this.musicPreplayed = true;
            Object.keys(this.audios).forEach((key) => {
                if (key !== 'bg') {
                    this.audios[key].play();
                    this.audios[key].pause();
                    this.audios[key].currentTime = 0;
                }
            });
        },
        playMusic(key) {
            if (key in this.audios
                && this.audios[key].paused
                && !this.muted) {
                this.audios[key].play();
            }
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
            const url = `${this.envData.host24}/jssdk/jssdkParams`;
            const params = {
                gameName: 'lightTour',
                domainUrl: location.href.split('#')[0]
            };
            axios({
                method: 'get',
                url,
                params
            }).then((response) => {
                const result = response.data;
                console.log(result);
                const config = result.data;
                this.setWx(config);
            }).catch((error) => {
                console.error(error);
            });
        },
        // 设置微信分享信息
        setWx(config) {
            wx.configWx(config, [
                // 所有要调用的 API 都要加到这个列表中
                'onMenuShareTimeline', 'onMenuShareAppMessage', 'hideMenuItems', 'scanQRCode', 'addCard'
            ]);
            const wxShareObj = {
                title: '你今晚去边？广州K11【Light Map】带你开启光感革新之旅',
                desc: '你今晚去边？快来体验Light Tour六大主题任务，点亮手机地图即可体验广州K11开业首展',
                link: 'http://lighttour-v.klub11.com/games/k11LightTour/1',
                imgUrl: 'https://static.cdn.24haowan.com/img/32/32152090772391067.jpg'
            };
            wx.setWxShare(wxShareObj, () => {
                this.$bus.$emit('shareCallback');
            });
        },
        // 获取是否为会员
        getIsMember() {
            axios({
                method: 'get',
                url: `${this.envData.host24}/member/isMember`
            }).then((response) => {
                const result = response.data;
                console.log(result);
                if (result.code === 0) {
                    if (result.data.isMember === 1) {
                        this.isMember = true;
                    }
                }
            });
        },
        // 获取地图标记信息
        getFloorComplete() {
            axios({
                method: 'get',
                url: `${this.envData.host24}/member/markAll`
            }).then((response) => {
                const result = response.data;
                console.log(result);
                if (result.code === 0) {
                    const mark = result.data.mark;
                    let count = 0;
                    Object.keys(mark).forEach((key) => {
                        if (mark[key] === 'yes') count += 1;
                    });
                    if (count >= 5) { // 已达成条件
                        this.getCard();
                    } else {
                        this.$bus.$emit('hideGiftButton');
                    }
                    if (mark.location1 === 'yes') this.$bus.$emit('goal', { no: 1, initial: true });
                    if (mark.location2 === 'yes') this.$bus.$emit('goal', { no: 2, initial: true });
                    if (mark.location3 === 'yes') this.$bus.$emit('goal', { no: 3, initial: true });
                    if (mark.location4 === 'yes') this.$bus.$emit('goal', { no: 4, initial: true });
                    if (mark.location5 === 'yes') this.$bus.$emit('goal', { no: 5, initial: true });
                    if (mark.location6 === 'yes') this.$bus.$emit('goal', { no: 6, initial: true });
                }
            });
        },
        // 获取用户已领取卡券列表
        getCard() {
            const url = `${this.envData.host24}/jssdk/userCardList`;
            const params = {
                cardid: this.envData.cardId,
                gameName: 'lightTour'
            };
            axios({
                method: 'GET',
                url,
                params
            }).then((response) => {
                const result = response.data;
                console.log(result);
                const cardList = result.data;
                if (cardList.length > 0) { // 已领取
                    this.$bus.$emit('hideGiftButton');
                    this.hasGotPrize = true;
                } else {
                    this.$bus.$emit('showGiftButton');
                }
            }).catch((error) => {
                console.error(error);
            });
        },
        // 添加卡券
        addCard(callback) {
            const url = `${this.envData.host24}/jssdk/addCard`;
            const params = {
                gameName: 'lightTour',
                cardid: this.envData.cardId
            };
            axios({
                method: 'get',
                url,
                params
            }).then((response) => {
                const result = response.data;
                console.log(result);
                const cardList = result.data;
                window.wx.addCard({
                    cardList: [
                        {
                            cardId: cardList[0].cardId,
                            cardExt: JSON.stringify(cardList[0].cardExt)
                        }
                    ], // 需要添加的卡券列表
                    success: () => {
                        if (typeof callback === 'function') callback();
                    }
                });
            }).catch((error) => {
                console.error(error);
            });
        },
        testCount() {
            this.testCountNum += 1;
            if (this.testCountNum === 10) {
                this.enableTest();
            }
        },
        enableTest() {
            const head = document.querySelectorAll('head')[0];
            const script = document.createElement('script');
            script.src = '//24haowan-cdn.shanyougame.com/public/js/vconsole.min.js';
            head.appendChild(script);
        }
    },
    mounted() {
        this.initEnvData();
        this.initAudios();
        this.initEvents();
        this.load();
        this.getWxConfig();
        this.getIsMember();
        this.getFloorComplete();
        window.bus = this.$bus;
        window.app = this;
    }
};
</script>

<style>
#app {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
}
</style>
