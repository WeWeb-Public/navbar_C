<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="navbar_C" ww-fixed>
        <div class="placeholder" v-if="section.data.appearPercent == null">
            <!-- wwManager:start -->
            <div class="placeholder-infos">
                Placeholder for the navigation bar
                <br>Place the navigation bar on top of the section list to hide this
            </div>
            <!-- wwManager:end -->
        </div>
        <div class="navbar-top" :class="{'show': show, 'no-anim': section.data.appearPercent == null, 'fixed-nav': section.data.appearPercent != -3, 'shadow': displayShadow, }">
            <div class="container" :class="{'show-bottom':   secondNavShow || editMode}">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->
                <!-- Weweb Wallpaper -->
                <wwObject class="background" :ww-object="section.data.background" ww-category="background"></wwObject>

                <wwLayoutColumn tag="div" ww-default="ww-row" :ww-list="section.data.rows" class="content" @ww-add="add(section.data.rows, $event)" @ww-remove="remove(section.data.rows, $event)">
                    <wwObject v-for="row in section.data.rows" :key="row.uniqueId" :ww-object="row"></wwObject>
                </wwLayoutColumn>
                <transition name="fade">
                    <wwLayoutColumn
                        v-if="!navbarCSecondPart && secondNavShow  || editMode"
                        tag="div"
                        ww-default="ww-row"
                        :ww-list="section.data.rowsBottom"
                        class="content"
                        @ww-add="add(section.data.rowsBottom, $event)"
                        @ww-remove="remove(section.data.rowsBottom, $event)"
                    >
                        <wwObject v-for="rowBottom in section.data.rowsBottom" :key="rowBottom.uniqueId" :ww-object="rowBottom"></wwObject>
                    </wwLayoutColumn>
                </transition>
            </div>
        </div>

        <div class="navbar-side">
            <div class="container-up">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->
                <wwObject class="background" :ww-object="section.data.backgroundSide" ww-category="background"></wwObject>

                <wwLayoutColumn
                    tag="div"
                    ww-default="ww-row"
                    :ww-list="section.data.rowsSide"
                    class="content"
                    @ww-add="add(section.data.rowsSide, $event)"
                    @ww-remove="remove(section.data.rowsSide, $event)"
                >
                    <wwObject v-for="row in section.data.rowsSide" :key="row.uniqueId" :ww-object="row"></wwObject>
                </wwLayoutColumn>
            </div>
        </div>
        <div class="navbar-cover" :class="{'show': navbarOpen}" @click="toggleNavbar"></div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
/* wwManager:start */
import navbarCAppearPercent from './navbarCAppearPercent.vue'
wwLib.wwPopups.addPopup('navbarCAppearPercent', navbarCAppearPercent);
wwLib.wwPopups.addStory('navbar_C_APPEAR_PERCENT', {
    title: {
        en: 'Scroll appear percent',
        fr: 'Pourcentage de scroll avant apparition'
    },
    type: 'navbarCAppearPercent',
    buttons: {
        FINISH: {
            text: {
                en: 'Finish',
                fr: 'Terminer'
            },
            next: false
        }
    }
});
/* wwManager:end */

export default {
    name: "__COMPONENT_NAME__",
    props: {
        // The section controller object is passed to you.
        sectionCtrl: Object
    },
    data() {
        return {
            windowHeight: 0,
            show: false,
            secondNavShow: false,
            navbarOpen: false,
            lastScrollTop: 0,
            displayShadow: false
        }
    },
    computed: {
        //Get the section object here !
        // It contains all the info and data about the section
        // Use it has you like !
        section() {
            return this.sectionCtrl.get();
        },
        scroll() {
            return document.body.scrollTop
        },
        navbarCSecondPart() {
            return this.section.data.secondPartToggle
        },
        editMode() {
            return this.sectionCtrl.getEditMode() == 'CONTENT'
        }
    },
    watch: {
        show() {
            if (this.show && this.$el) {
                let height = this.$el.querySelector('.navbar-top').getBoundingClientRect().height;

                wwLib.wwNavbar.updateHeight(70);
            }
            else {
                wwLib.wwNavbar.updateHeight(0);
            }
        },
        secondNavShow() {
            if (this.secondNavShow && this.$el) {
                let height = this.$el.querySelector('.navbar-top').getBoundingClientRect().height;
                wwLib.wwNavbar.updateHeight(140);
            }
            else {
                wwLib.wwNavbar.updateHeight(0);
            }
        }

    },
    methods: {
        init() {
            window.addEventListener('scroll', this.onScroll);
            window.addEventListener('resize', this.onResize);

            wwLib.$on('wwNavbar:toggle', this.toggleNavbar);

            this.onResize();

        },

        /*=============================================m_ÔÔ_m=============================================\
          TOGGLE NAVBAR SIDE
        \================================================================================================*/

        toggleNavbar() {
            this.navbarOpen = !this.navbarOpen;
            if (this.navbarOpen) {
                for (let section of document.querySelectorAll('.ww-section:not([ww-fixed])')) {
                    section.classList.add('navbar_C-open');
                }
                for (let container of this.$el.querySelectorAll('.container-up')) {
                    container.classList.add('navbar_C-open');

                }
                this.$el.querySelector('.container').classList.add('navbar_C-open');
            }
            else {
                for (let section of document.querySelectorAll('.ww-section:not([ww-fixed])')) {
                    section.classList.remove('navbar_C-open');
                }
                for (let container of this.$el.querySelectorAll('.container-up')) {
                    container.classList.remove('navbar_C-open');
                }
                this.$el.querySelector('.container').classList.remove('navbar_C-open');
            }
        },

        /*=============================================m_ÔÔ_m=============================================\
          SHOW / HIDE NAVBAR TOP
        \================================================================================================*/
        onScroll() {
            const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop);
            if ((window.location.pathname == '/ww_front/' || window.location.pathname == '/') && scrollTop == 0) {
                this.displayShadow = false;
            } else {
                this.displayShadow = true;
            }

            if (this.section.data.appearPercent == -2) {
                this.setScrollUp();
            } else if (this.section.data.appearPercent == -3) {
                this.show = false;
            } else {
                this.setScrollPercent();
            }
            this.setSecondBarScrollPercent()

        },
        onResize() {
            const e = document.documentElement;
            const g = document.getElementsByTagName('body')[0];
            this.windowHeight = window.innerHeight || e.clientHeight || g.clientHeight;

            this.onScroll()
        },
        setScrollPercent() {
            if (this.section.data.appearPercent == null) {
                this.show = true;
                return;
            }
            const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop);
            let scrollPercent = Math.max(0, 100 * scrollTop / this.windowHeight) - 0.0001;

            this.show = scrollPercent >= this.section.data.appearPercent;
        },
        setSecondBarScrollPercent() {
            if (this.section.data.secondPartAppearPercent == null) {
                this.secondNavShow = true;
                return;
            }
            const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop);
            let scrollPercent = Math.max(0, 100 * scrollTop / this.windowHeight) - 0.0001;

            this.secondNavShow = scrollPercent >= this.section.data.secondPartAppearPercent
        },
        setScrollUp() {

            const lastScrollTop = this.lastScrollTop || 0
            const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop);

            const scrollPercent = Math.max(0, 100 * scrollTop / this.windowHeight) - 0.0001;

            this.show = (lastScrollTop > 5) ? scrollPercent < lastScrollTop : false

            this.lastScrollTop = scrollPercent
        },

        /*=============================================m_ÔÔ_m=============================================\
          ADD / REMOVE ROWS
        \================================================================================================*/
        /* wwManager:start */
        add(list, options) {
            list.splice(options.index, 0, options.wwObject);

            this.sectionCtrl.update(this.section);
        },
        remove(list, options) {
            list.splice(options.index, 1);

            this.sectionCtrl.update(this.section);
        },

        async openOptions() {
            let needUpdate = false
            let options = {
                firstPage: 'navbar_C_APPEAR_PERCENT',
                data: {
                    navbarCPercent: this.section.data.appearPercent,
                    navbarCscrollSecondAnim: this.section.data.secondPartAppearPercent,
                    navbarCtoggleValue: this.section.data.secondPartToggle,
                },
            }

            try {
                const result = await wwLib.wwPopups.open(options)
                console.log('result:', result)

                if (typeof (result.navbarCPercent) !== 'undefinded') {
                    this.section.data.appearPercent = result.navbarCPercent;
                    needUpdate = true
                }
                if (typeof (result.navbarCscrollSecondAnim) !== 'undefinded') {
                    if (!isNaN(result.navbarCscrollSecondAnim)) {
                        this.section.data.secondPartAppearPercent = result.navbarCscrollSecondAnim;
                        needUpdate = true
                    }
                }
                if (typeof (result.navbarCtoggleValue) !== 'undefinded') {
                    this.section.data.secondPartToggle = result.navbarCtoggleValue;
                    needUpdate = true
                }
                if (needUpdate) {
                    this.sectionCtrl.update(this.section);
                    this.onScroll();
                }
            } catch (error) {
                console.error(error)
            }
        }
        /* wwManager:end */
    },
    mounted() {
        this.init();
    },
    created() {

        let needUpdate = false;

        //Initialize section data
        this.section.data = this.section.data || {};

        if (!this.section.data.appearPercent) {
            this.section.data.appearPercent = null;
            needUpdate = false;
        }
        if (!this.section.data.secondPartAppearPercent) {
            this.section.data.secondPartAppearPercent = 30;
            needUpdate = false;
        }
        if (!this.section.data.secondPartToggle) {
            this.section.data.secondPartToggle = false;
            needUpdate = false;
        }

        if (!this.section.data.rows) {
            this.section.data.rows = [];
            needUpdate = true;
        }

        if (!this.section.data.rowsBottom) {
            this.section.data.rowsBottom = [];
            needUpdate = true;
        }
        if (!this.section.data.rowsSide) {
            this.section.data.rowsSide = [];
            needUpdate = true;
        }
        if (!this.section.data.background) {
            this.section.data.background = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
            needUpdate = true;
        }
        if (!this.section.data.backgroundSide) {
            this.section.data.backgroundSide = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
            needUpdate = true;
        }

        if (needUpdate) {
            this.sectionCtrl.update(this.section);
        }

        this.onResize();
    },
    beforeDestroy() {
        window.removeEventListener('scroll', this.onScroll);
        window.removeEventListener('resize', this.onResize);

        wwLib.$off('wwNavbar:toggle', this.toggleNavbar);
    }
};
</script>``

<style lang="scss">
.navbar_C-open {
    transform: translateY(-20px);
}

.ww-section:not([ww-fixed]) {
    transition: all 0.5s ease;
}
</style>

<style scoped lang="scss">
.navbar_C {
    width: 100%;

    .placeholder {
        min-height: 70px;
        position: relative;

        /* wwManager:start */
        .placeholder-infos {
            position: absolute;
            top: 0;
            left: 0;
            bottom: 0;
            right: 0;
            background-color: #d6d6d6;
            display: flex;
            justify-content: center;
            align-items: center;
            color: black;
            text-align: center;
        }
        /* wwManager:end */
    }

    .navbar-cover {
        display: none;
        pointer-events: all;
        width: 100%;
        position: fixed;
        top: 0;
        bottom: 0;
        z-index: 100;

        &.show {
            display: block;
        }
    }
    .navbar-top {
        width: 100%;
        // position: fixed;
        top: 0;
        z-index: 101;

        transition: all 0.5s ease;
        //transition: transform 0.3s ease;
        // transform: translateY(calc(-100% - 10px));

        &.fixed-nav {
            position: fixed;
            transform: translateY(calc(-100% - 10px));
        }
        &.show {
            transform: translateY(0);
        }
        &.shadow {
            box-shadow: 0 2px 10px 0 rgba(74, 74, 74, 0.2);
        }

        .container {
            width: 100%;
            height: 70px;
            transition: all 0.5s ease-in;
            &.show-bottom {
                @media (min-width: 1024px) {
                    height: 140px;
                }
            }
            &.navbar_C-open {
                transform: translateY(-20px);
            }

            &.no-anim {
                transition: none;
            }
            &.second-navbar {
                transition: all 5s ease;
            }
            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }
            .content {
                position: relative;
            }
        }
    }

    .navbar-side {
        position: fixed;
        top: 100%;
        //top: 0;
        left: 0;
        width: 100%;
        z-index: 101;
        height: 100%;

        .container-up {
            width: 100%;
            height: calc(100% + 2px);
            transition: all 0.3s ease;
            transform: translateY(-50%);
            opacity: 0;
            visibility: hidden;

            &.navbar_C-open {
                transform: translateY(calc(-100% + 1px));

                opacity: 1;
                visibility: visible;
            }

            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }

            .content {
                position: relative;
                overflow-y: scroll;
                overflow-x: hidden;
                pointer-events: all;
                height: 100%;
            }

            .edit-menu-container {
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
                height: 60px;
                overflow-x: hidden;
            }
        }
    }
    /* Enter and leave animations can use different */
    /* durations and timing functions.              */
    .fade-enter-active {
        transition: all 0.2s ease 0.3s;
    }
    .fade-leave-active {
        //transition: all 0.4s ease;
    }
    .fade-enter, .fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
        opacity: 0;
    }
}
</style>
