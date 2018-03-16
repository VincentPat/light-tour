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
import { Base64 } from 'js-base64';

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
            isMember: true, // 是否为会员
            progress: 0, // 加载进度
            timestamp: Date.now(), // 进入页面的时间
            envData: {}, // 运行环境参数
            imgs: [], // 图片
            audios: {}, // 音频
            audiosSrc: {
                // bg: 'https://static.cdn.24haowan.com/music/32/321520852919.mp3',
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
                    cardId: 'pC2Vgv8HOaMpzoY8v9MH9TTi3AOc',
                    host24: 'http://test.ac.24haowan.com'
                };
            } else if (this.env === 'production') {
                this.envData = {
                    host: 'http://lighttour-v.klub11.com',
                    account_id: '6',
                    apiKey: '155152110859733',
                    apiSecret: 'C7IZh37L',
                    interfaceId: 'a4e1d7e32123751f911a',
                    skey: 'b1603307',
                    cardId: 'pC2Vgv8HOaMpzoY8v9MH9TTi3AOc',
                    host24: 'http://api.24haowan.com'
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
                wx.scanQRCode({
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
                    if (result.code === 0) {
                        this.$bus.$emit('goal', this.currentFloor);
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
            this.$bus.$on('goal', (no) => {
                if (this.completedFloors.indexOf(no) === -1) {
                    this.completedFloors.push(no);
                    this.playMusic('goal');
                    this.$bus.$emit('showPoint', 'goal');
                }
                if (this.completedFloors.length >= 5
                    && !this.hasGotPrize) { // 达成条件
                    setTimeout(() => {
                        this.$bus.$emit('complete');
                    }, 3500);
                }
            });
            this.$bus.$on('showPoint', () => {
                this.showPoint = true;
                setTimeout(() => {
                    this.$bus.$emit('hidePoint');
                }, 3000);
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
            });
            // 分享回调
            this.$bus.$on('shareCallback', () => {
                window._hmt.push(['_trackEvent', 'share', 'share', 'share']);
                this.$bus.$emit('hideShare');
                axios({
                    method: 'get',
                    url: `${this.envData.host24}/member/share`
                }).then((response) => {
                    const result = response.data;
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
                    location.href = `https://app.klub11.com/?r=page/auth&account_id=6&origin=7&css=2&_redirecturl=${encodeURIComponent(location.href.split('#')[0])}`;
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
            const url = `${this.envData.host}/v1/wechat-js/js-config`;
            const timestamp = Date.now();
            const params = {
                apiKey: this.envData.apiKey,
                interfaceId: this.envData.interfaceId,
                timestamp,
                account_id: this.envData.account_id,
                domain_url: location.href.split('#')[0]
            };
            const sign = this.genSign(params);
            params.sign = sign;
            axios({
                method: 'get',
                url,
                params,
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                }
            }).then((response) => {
                const result = this.decodeReturnData(response.data);
                const config = result.data;
                this.setWx(config);
            }).catch((error) => {
                console.error(error);
            });
        },
        // 设置微信分享信息
        setWx(config) {
            wx.configWx(config);
            const wxShareObj = {
                title: '今晚去边？广州K11开业地图带你闯关光感革新之旅',
                desc: '今晚去边？闯关六大主题空间，完成任务，点亮地图即可体验K11开业首展',
                link: location.href.split('#')[0],
                imgUrl: 'https://static.cdn.24haowan.com/img/32/32152090772391067.jpg'
            };
            wx.setWxShare(wxShareObj, () => {
                this.$bus.$emit('shareCallback');
            });
        },
        // 解析K11的返回数据
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
        },
        // 生成签名
        genSign(params) {
            const data = Object.assign({
                apiSecret: this.envData.apiSecret
            }, params);
            let paramStr = '';
            const keys = Object.keys(data).sort();
            keys.forEach((key) => {
                paramStr = `${paramStr}${key}+${data[key]}`;
            });
            return md5(paramStr);
        },
        // 获取是否为会员
        getIsMember() {
            axios({
                method: 'get',
                url: `${this.envData.host24}/member/isMember`
            }).then((response) => {
                const result = response.data;
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
                if (result.code === 0) {
                    // if (result.data.markAll === 1) { // 全部标记
                    //     this.$bus.$emit('showGiftButton');
                    // }
                    const mark = result.data.mark;
                    let count = 0;
                    Object.keys(mark).forEach((key) => {
                        if (mark[key] === 'yes') count += 1;
                    });
                    if (count >= 5) { // 已达成条件
                        const url = `${this.envData.host}/v1/wechat-js/user-card-list`;
                        const timestamp = Date.now();
                        const params = {
                            apiKey: this.envData.apiKey,
                            interfaceId: this.envData.interfaceId,
                            timestamp,
                            account_id: this.envData.account_id
                        };
                        let openid = location.href.match(/openid=\w+/ig)[0];
                        openid = openid.substr(7);
                        const data = {
                            cardId: this.envData.cardId,
                            openid
                        };
                        const sign = this.genSign(Object.assign({}, params, data));
                        params.sign = sign;
                        axios({
                            method: 'POST',
                            url,
                            params,
                            data,
                            headers: {
                                'Content-Type': 'application/x-www-form-urlencoded'
                            }
                        }).then((response2) => {
                            const result2 = this.decodeReturnData(response2.data);
                            const cardList = result2.data;
                            if (cardList.length > 0) { // 已领取
                                this.$bus.$emit('hideGiftButton');
                                this.hasGotPrize = true;
                            } else {
                                this.$bus.$emit('showGiftButton');
                            }
                        }).catch((error) => {
                            console.error(error);
                        });
                    } else {
                        this.$bus.$emit('hideGiftButton');
                    }
                    if (mark.location1 === 'yes') this.completedFloors.push(1);
                    if (mark.location2 === 'yes') this.completedFloors.push(2);
                    if (mark.location3 === 'yes') this.completedFloors.push(3);
                    if (mark.location4 === 'yes') this.completedFloors.push(4);
                    if (mark.location5 === 'yes') this.completedFloors.push(5);
                    if (mark.location6 === 'yes') this.completedFloors.push(6);
                }
            });
        },
        // 添加卡券
        addCard(callback) {
            const url = `${this.envData.host}/v1/wechat-js/card-ext-config`;
            const timestamp = Date.now();
            const params = {
                apiKey: this.envData.apiKey,
                interfaceId: this.envData.interfaceId,
                timestamp,
                account_id: this.envData.account_id,
                cardId: this.envData.cardId
            };
            const sign = this.genSign(params);
            params.sign = sign;
            axios({
                method: 'get',
                url,
                params,
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                }
            }).then((response) => {
                const result = this.decodeReturnData(response.data);
                const cardList = result.data;
                window.wx.addCard({
                    cardList, // 需要添加的卡券列表
                    success: () => {
                        if (typeof callback === 'function') callback();
                    }
                });
            }).catch((error) => {
                console.error(error);
            });
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
