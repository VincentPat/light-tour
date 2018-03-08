<template>
    <div id="app">
        <transition name="fade" appear>
            <loading v-show="showLoading"></loading>
        </transition>
        <home v-show="showHome"></home>
    </div>
</template>

<script>
import loading from './components/loading';
import home from './components/home';

export default {
    name: 'App',
    components: {
        loading,
        home
    },
    data() {
        return {
            showLoading: true,
            showHome: false
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
        },
        load() {
            setTimeout(() => {
                this.$bus.$emit('progress', 100);
            }, 2000);
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
