<template>
    <div class="navbar-a-appear-percent">
        <div class="title">{{wwLang.getText('scrollTitle')}}</div>
        <div class="desc">{{wwLang.getText('scrollDesc')}}</div>
        <div class="desc">{{wwLang.getText('scrollDesc2')}}</div>
        <wwManagerInput class="input" color="green" v-model="scrollPercent" :label="wwLang.getText('percent')" @change="setScrollPercent($event)"></wwManagerInput>

        <div class="second_title">{{wwLang.getText('radioTitle')}}</div>
        <wwManagerRadio class="input" :value="toggleValue" @change="selectValue($event)"></wwManagerRadio>

        <div class="second_title">{{wwLang.getText('afterSectionTitle')}}</div>
        <div class="second_desc">{{wwLang.getText('scrollDesc')}}</div>
        <wwManagerInput class="input" color="green" v-model="scrollSecondAnim" :label="wwLang.getText('percent')" @change="setScrollSecondAnim($event)"></wwManagerInput>
    </div>
</template>

<script> 
import langPopupNavbarC from './langPopupNavbarC.json';

export default {
    name: "navbarCApearPercent",
    props: {
        options: {
            type: Object,
            default: function () {
                return {}
            }
        },
    },
    data() {
        return {
            wwLang: wwLib.wwManagerLang.use(langPopupNavbarC),
            scrollPercent: -1,
            scrollSecondAnim: 0,
            toggleValue: false
        };
    },
    computed: {
    },
    watch: {
    },
    methods: {
        init() {
            this.scrollPercent = this.options.data.navbarCPercent || -1;
            this.options.result.navbarCPercent = this.options.data.navbarCPercent || -1;

            this.scrollSecondAnim = this.options.data.navbarCscrollSecondAnim || 30;
            this.options.result.navbarCscrollSecondAnim = this.options.data.navbarCscrollSecondAnim || 30;

            this.toggleValue = this.options.data.navbarCtoggleValue || false;
            this.options.result.navbarCtoggleValue = this.options.data.navbarCtoggleValue || false;
        },
        setScrollPercent(value) {
            this.scrollPercent = value;
            this.options.result.navbarCPercent = value == -1 ? null : value;
        },
        setScrollSecondAnim(value) {
            this.scrollSecondAnim = value;
            this.options.result.navbarCscrollSecondAnim = value
        },
        selectValue(value) {
            this.toggleValue = value
            this.options.result.navbarCtoggleValue = value
        },
    },
    mounted() {
        this.init()
    },
    beforeDestroy() {

    }
};
</script>

<style scoped lang="scss">
.navbar-a-appear-percent {
    padding: 30px;
    font-family: "Monserrat", sans-serif;

    .title {
        margin-bottom: 20px;
    }
    .second_title {
        margin-top: 10px;
    }
    .desc {
        color: #a7a7a7;
        margin-bottom: 10px;
    }
    .second_desc {
        color: #a7a7a7;
        margin-top: 10px;
    }
    .input {
        margin-top: 20px;
    }
}
</style>
