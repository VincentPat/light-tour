<template>
    <div id="app">
        <transition name="fade" appear>
            <loading v-show="showLoading"></loading>
        </transition>
        <home v-show="showHome"></home>
        <transition name="fade">
            <share v-show="showShare"></share>
        </transition>
    </div>
</template>

<script>
import loading from './components/loading';
import home from './components/home';
import share from './components/share';

export default {
    name: 'App',
    components: {
        loading,
        home,
        share
    },
    data() {
        return {
            showLoading: true,
            showHome: false,
            showShare: false
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
            this.$bus.$on('showShare', () => {
                this.showShare = true;
            });
            this.$bus.$on('hideShare', () => {
                this.showShare = false;
            });
        },
        load() {
            setTimeout(() => {
                this.$bus.$emit('progress', 100);
            }, 100);
        }
    },
    mounted() {
        this.initEvents();
        this.load();
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
