<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="learn">
        <!-- wwManager:start -->
        <wwSectionEditMenu v-bind:sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
        <!-- wwManager:end -->
  
        <div class="header">
            <wwObject class="image" ww-category="background" v-bind:ww-object="section.data.headerBg"></wwObject>
        </div>

        <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.headerContent" class="list" @ww-add="add(section.data.headerContent, $event)" @ww-remove="remove(section.data.headerContent, $event)">
            <wwObject tag="div" v-for="object in section.data.headerContent" :key="object.uniqueId" :ww-object="object"></wwObject>
        </wwLayoutColumn>

        <div class="container">
            <div class="sticky">
                <div v-for="(menu, index) in section.data.menus" :key="menu.id" class="elem-container" :class="{ selected: section.data.menuIdSelected === menu.id }">
                    <!-- wwManager:start -->
                    <wwContextMenu class="ww-orange-button" tag="div" :options="stickyOptions" @select="menuSelect(index)" @remove="remove(section.data.menus, { index })" @addBefore="menuAdd(index)" @addAfter="menuAdd(index+1)" @link="menuLink(index)" v-if="sectionCtrl.getEditMode() == 'CONTENT'">
                        <wwOrangeButton></wwOrangeButton>
                    </wwContextMenu>
                    <!-- wwManager:end -->
                    <div class="elem" @click="goToPage(menu.link)" :class="{ cursor: menu.link }">
                        <div class="elem-border"></div>
                        <div class="elem-background"></div>
                        <div class="elem-text">
                            <wwObject :ww-object="menu.text"></wwObject>
                        </div>
                    </div>
                </div>
            </div>

            <div class="dropdown-mobile" v-if="menuIdSelected">
                <div class="select">
                    <select name="menus-select" v-model="menuIdSelected" @change="goToPage(menuSelected.link)">
                        <option v-for="menu in section.data.menus" :key="menu.id" :value="menu.id">
                            <wwObject :ww-object="menu.text"></wwObject>
                        </option>
                    </select>
                    <div class="text"></div>
                    <i class="wwi wwi-chevron-down"></i>
                </div>
            </div>

            <div class="content" v-if="section.data.isVideos">
                <div class="videos">
                    <div v-for="(elem) in section.data.elems" :key="elem.id">
                        <transition name="fade-delay" mode="in-out">
                            <div class="video" v-if="elem.id === elemSelected.id">
                                <wwObject :ww-object="elem.video" ></wwObject>
                            </div>
                        </transition>
                    </div>
                </div>

                <div v-for="(elem, index) in section.data.elems" :key="elem.id" class="elem-container" :class="{ selected: elem.id === elemSelected.id }">
                    <!-- wwManager:start -->
                    <wwContextMenu class="ww-orange-button" tag="div" :options="elemOptions" @select="elemSelect(index)" @remove="remove(section.data.elems, { index })" @addBefore="elemAdd(index)" @addAfter="elemAdd(index+1)" v-if="sectionCtrl.getEditMode() == 'CONTENT'">
                        <wwOrangeButton></wwOrangeButton>
                    </wwContextMenu>
                    <!-- wwManager:end -->
                    <div class="elem" @click="elemSelected = elem">
                        <div class="elem-border"></div>
                        <div class="elem-background"></div>
                        <div class="elem-row">
                            <div class="icon"><i class="wwi wwi-play"></i></div>
                            <wwObject class="row" :ww-object="elem.row"></wwObject>
                        </div>
                    </div>
                </div>

                <div class="elem-content">
                    <div v-for="(elem) in section.data.elems" :key="elem.id">
                        <transition name="fade" mode="in-out">
                            <div v-if="elem.id === elemSelected.id">
                                <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="elem.content" @ww-add="add(elem.content, $event)" @ww-remove="remove(elem.content, $event)">
                                    <wwObject tag="div" v-for="object in elem.content" :key="object.uniqueId" :ww-object="object"></wwObject>
                                </wwLayoutColumn>
                            </div>
                        </transition>
                    </div>
                </div>
            </div>

            <div class="content" v-else>
                <div class="elem-content">
                    <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.contentNoVideo" @ww-add="add(section.data.contentNoVideo, $event)" @ww-remove="remove(section.data.contentNoVideo, $event)">
                        <wwObject tag="div" v-for="object in section.data.contentNoVideo" :key="object.uniqueId" :ww-object="object"></wwObject>
                    </wwLayoutColumn>
                </div>
            </div>
        </div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>

/* wwManager:start */
wwLib.wwPopups.addStory('LEARN_OPTION_SECTION', {
    title: {
        en: 'Section Config',
        fr: `Configuration de la section`
    },
    type: 'wwPopupForm',
    storyData: {
        fields: [{
            label: {
                en: 'Elements list',
                fr: `Liste d'éléments`
            },
            type: 'radio',
            key: 'isVideos',
            valueData: 'isVideos'
        }]
    },
    buttons: {
        OK: {
            text: {
                en: 'Ok',
                fr: 'Valider'
            },
            next: false
        }
    }
})
/* wwManager:end */
export default {
    name: "__COMPONENT_NAME__",
    props: {
        // The section controller object is passed. You can access it anytime
        sectionCtrl: Object
    },
    data() {
        return {
            wwLang: wwLib.wwLang,
            elemSelected: {
                video: undefined,
                content: []
            },
            menuIdSelected: undefined,
            /* wwManager:start */
            stickyOptions: {
                items: [
                    {
                        text: {
                            en: 'Select',
                            fr: 'Selectionner'
                        },
                        icon: 'wwi wwi-check',
                        action: 'select'
                    },
                    {
                        text: {
                            en: 'Before',
                            fr: 'Avant'
                        },
                        icon: 'wwi wwi-add',
                        action: 'addBefore'
                    },
                    {
                        text: {
                            en: 'After',
                            fr: 'Apres'
                        },
                        icon: 'wwi wwi-add',
                        action: 'addAfter'
                    },
                    {
                        text: {
                            en: 'Delete',
                            fr: 'Supprimer'
                        },
                        icon: 'wwi wwi-delete',
                        action: 'remove'
                    },
                    {
                        text: {
                            en: 'Link',
                            fr: 'Lien'
                        },
                        icon: 'wwi wwi-link-page',
                        action: 'link'
                    }
                ]
            },
            elemOptions: {
                items: [
                    {
                        text: {
                            en: 'Select',
                            fr: 'Selectionner'
                        },
                        icon: 'wwi wwi-check',
                        action: 'select'
                    },
                    {
                        text: {
                            en: 'Before',
                            fr: 'Avant'
                        },
                        icon: 'wwi wwi-add',
                        action: 'addBefore'
                    },
                    {
                        text: {
                            en: 'After',
                            fr: 'Apres'
                        },
                        icon: 'wwi wwi-add',
                        action: 'addAfter'
                    },
                    {
                        text: {
                            en: 'Delete',
                            fr: 'Supprimer'
                        },
                        icon: 'wwi wwi-delete',
                        action: 'remove'
                    }
                ]
            }
            /* wwManager:end */
        }
    },
    computed: {
        // The section object contains all the info and data about the section
        // Use it as you like !
        section() {
            return this.sectionCtrl.get();
        },
        menuSelected() {
            for (const menu of this.section.data.menus) {
                if (this.menuIdSelected === menu.id)
                return menu
            }
            return undefined
        }
    },
    created() {
        // Initialize the data once the section is created in the DOM
        this.init()
    },
    methods: {
        init() {
            // Initialize section data
            let needUpdate = false

            // We will only save the data in this.section.data
            // So you need to put in there all the data of you WeWeb objects
            this.section.data = this.section.data || {};

            if (!this.section.data.headerBg) {
                this.section.data.headerBg = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }
            if (!this.section.data.headerContent) {
                this.section.data.headerContent = []
                needUpdate = true
            }

            if (!this.section.data.menus) {
                this.section.data.menus = []
                this.menuAdd(0)
                needUpdate = true
            }

            if (!this.section.data.elems) {
                this.section.data.elems = []
                this.elemAdd(0)
                needUpdate = true
            }

            if (!this.section.data.menuIdSelected) {
                this.section.data.menuIdSelected = undefined
                needUpdate = true
            }

            if (this.section.data.isVideos === undefined) {
                this.section.data.isVideos = true
                needUpdate = true
            }

            if (!this.section.data.contentNoVideo) {
                this.section.data.contentNoVideo = []
                needUpdate = true
            }

            this.elemSelected = this.section.data.elems[0]
            this.menuIdSelected = this.section.data.menuIdSelected
            
            if (needUpdate) {
                this.sectionCtrl.update(this.section);
            }
        },
        elemSelect(index) {
            try {
                this.elemSelected = this.section.data.elems[index]
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        goToPage(pageId) {
            const path = wwLib.wwWebsiteData.getPageRoute(pageId, true) || '/';
            wwLib.$router.push(path);
            this.$emit('next', null);
        },

        // --------- EDITOR FUNCTIONS ---------
        // All the codes between /* wwManager:start */ and /* wwManager:end */ are only for editor purposes
        // So It won't in the published website!
        /* wwManager:start */
        elemAdd(index) {
            try {
                const copy = this.section.data.elems[0]
                const data = {
                    id: wwLib.wwUtils.getUniqueId(),
                    video: wwLib.wwObject.getDefault({ type: 'ww-video' }),
                    row: wwLib.wwObject.getDefault({ type: 'ww-row' }),
                    content: []
                }
                this.section.data.elems.splice(index, 0, data);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        menuAdd(index) {
            try {
                const data = {
                    id: wwLib.wwUtils.getUniqueId(),
                    text: wwLib.wwObject.getDefault({ type: 'ww-text' })
                }
                this.section.data.menus.splice(index, 0, data);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        menuSelect(index) {
            try {
                this.section.data.menuIdSelected = this.section.data.menus[index].id
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        async menuLink(index) {
            let options = {
                firstPage: 'LINK_INTERNAL',
                data: { linkPage: this.section.data.menus[index].link }
            }
            try {
                const result = await wwLib.wwPopups.open(options)
                if (typeof(result.linkPage) !== 'undefined') {
                    this.section.data.menus[index].link = result.linkPage
                }
                this.sectionCtrl.update(this.section)
            } catch (err) {
                wwLib.wwLog.error(err)
            }
        },
        add(list, options) {
            try {
                list.splice(options.index, 0, options.wwObject);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        remove(list, options) {
            try {
                list.splice(options.index, 1);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        async openOptions() {
            let options = {
                firstPage: 'LEARN_OPTION_SECTION',
                data: { isVideos: this.section.data.isVideos }
            }
            try {
                const result = await wwLib.wwPopups.open(options)

                if (typeof(result.isVideos) !== 'undefined') {
                    this.section.data.isVideos = result.isVideos
                }
                this.sectionCtrl.update(this.section)
            } catch (err) {
                wwLib.wwLog.error(err)
            }
        }
        /* wwManager:end */
    }
};
</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style lang="scss" scoped>
// --------- GLOBAL CSS ---------
.learn {
  a {
    text-decoration: none;
    color: inherit;
  }
  .cursor {
    cursor: pointer;
  }
}

// --------- EDITOR CSS ---------
.learn {
  position: relative;
  .header {
    position: absolute;
    width: 100%;
    height: 180px;
    border-bottom-left-radius: 50%;
    border-bottom-right-radius: 50%;
    -webkit-clip-path: ellipse(70% 70% at 50% 30%);
    clip-path: ellipse(70% 70% at 50% 30%);
    @media (min-width: 768px) {
      height: 250px;
    }
    @media (min-width: 992px) {
      height: 300px;
    }
    @media (min-width: 1200px) {
      height: 400px;
    }
    .image {
      position: absolute;
      top: 0;
      left: 0;
      height: 100%;
      width: 100%;
    }
  }
  .container {
    position: relative;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -ms-flex-wrap: wrap;
    flex-wrap: wrap;
    margin: 50px auto;
    .sticky {
      pointer-events: all;
      position: -webkit-sticky;
      position: sticky;
      bottom: 50px;
      left: 0;
      display: none;
      -ms-flex-item-align: end;
      align-self: flex-end;
      height: -webkit-fit-content;
      height: -moz-fit-content;
      height: fit-content;
      -webkit-box-shadow: 0 1px 23px -5px rgba(0, 0, 0, 0.2);
      box-shadow: 0 1px 23px -5px rgba(0, 0, 0, 0.2);
      border-radius: 10px;
      margin-left: 2%;
      .elem-container {
        position: relative;
        height: 100%;
        width: 100%;
        .elem {
          position: relative;
          display: block;
          overflow: hidden;
          width: 225px;
          z-index: 1;
          background: white;
          .elem-text {
            display: block;
            padding: 30px 20px;
            color: #5e5e5e;
            font-size: 15px;
            font-weight: 500;
            line-height: 18px;
          }
          .elem-border {
            position: absolute;
            top: 0;
            width: 100%;
            height: 100%;
            border: 1px solid #dedede;
            z-index: -2;
          }
          .elem-background {
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            width: 100%;
            z-index: -1;
            background: -webkit-gradient(
              linear,
              left top,
              right top,
              from(#455fa2),
              to(#2d84c1)
            );
            background: linear-gradient(90deg, #455fa2 0%, #2d84c1 100%);
            display: none;
          }
        }
        &:first-child {
          .elem,
          .elem-border {
            border-top-left-radius: 10px;
            border-top-right-radius: 10px;
          }
        }
        &:last-child {
          .elem,
          .elem-border {
            border-bottom-left-radius: 10px;
            border-bottom-right-radius: 10px;
          }
        }
        &:hover,
        &.selected {
          .elem {
            .elem-text {
              color: white;
            }
            .elem-background {
              display: block;
            }
          }
        }
      }
    }
    .dropdown-mobile {
      pointer-events: all;
      display: -webkit-box;
      display: -ms-flexbox;
      display: flex;
      -webkit-box-pack: center;
      -ms-flex-pack: center;
      justify-content: center;
      width: 100%;
      .select {
        select {
          display: block;
          width: 100%;
          border: none;
          -moz-appearance: none;
          appearance: none;
          -webkit-appearance: none;
          -moz-apparance: none;
          -o-appearance: none;
          appearance: none;
          outline: none;
          color: white;
          width: 100%;
          height: 100%;
          position: absolute;
          top: 0;
          left: 0;
          background: none;
          padding: inherit;
          padding: 2px 50px 2px 25px;
          cursor: pointer;
          font-size: 15px;
          font-weight: bold;
          line-height: 30px;
        }
        i {
          width: 25px;
          height: 25px;
          font-size: 25px;
        }
        padding: 2px 20px 2px 25px;
        position: relative;
        pointer-events: all;
        display: -webkit-box;
        display: -ms-flexbox;
        display: flex;
        -webkit-box-pack: justify;
        -ms-flex-pack: justify;
        justify-content: space-between;
        -webkit-box-align: center;
        -ms-flex-align: center;
        align-items: center;
        color: white;
        margin: 20px 0;
        width: 250px;
        height: 60px;
        border-radius: 10px;
        cursor: pointer;
        background: -webkit-gradient(
          linear,
          left top,
          right top,
          from(#455fa2),
          to(#2d84c1)
        );
        background: linear-gradient(90deg, #455fa2 0%, #2d84c1 100%);
      }
    }
    .content {
      position: relative;
      width: 90%;
      margin: 0 5%;
      .videos {
        position: relative;
        background: white;
        .video {
          background: white;
        }
      }
      .elem-container {
        position: relative;
        width: 100%;
        .elem {
          pointer-events: all;
          position: relative;
          display: block;
          overflow: hidden;
          z-index: 1;
          cursor: pointer;
          -webkit-transition: color 0.5s ease;
          transition: color 0.5s ease;
          background: white;
          border-bottom: 5px solid #dedede;
          .elem-row {
            border-right: 2px solid #dedede;
            border-left: 2px solid #dedede;
            color: #5e5e5e;
            font-size: 18px;
            font-weight: 500;
            line-height: 22px;
            -webkit-transition: color 0.5s ease;
            transition: color 0.5s ease;
            display: -webkit-box;
            display: -ms-flexbox;
            display: flex;
            -webkit-box-align: center;
            -ms-flex-align: center;
            align-items: center;
            .icon {
              padding-left: 15px;
              font-size: 25px;
            }
            .row {
              width: 100%;
            }
          }
          .elem-background {
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            width: 100%;
            z-index: -1;
            background: -webkit-gradient(
              linear,
              left top,
              right top,
              from(#455fa2),
              to(#2d84c1)
            );
            background: linear-gradient(90deg, #455fa2 0%, #2d84c1 100%);
            -webkit-transform: translateX(-101%);
            transform: translateX(-101%);
          }
        }
        &:hover,
        &.selected {
          .elem {
            .elem-row {
              color: white;
            }
            .elem-background {
              -webkit-animation: slide-right .4s forwards 0.2s;
              animation: slide-right .4s forwards 0.2s;
            }
          }
        }
      }
      .elem-content {
        position: relative;
        margin: 0 -3%;
      }
    }
  }
  @media (min-width: 1024px) {
    .container {
      .sticky {
        margin-left: none;
        .elem-container {
          .elem {
            width: 280px;
          }
        }
      }
      .content {
        width: 80%;
        margin: 0 10%;
        .elem-container {
          .elem {
            .elem-row {
              .icon {
                padding-left: 45px;
              }
            }
          }
        }
      }
    }
  }
  @media (min-width: 768px) {
    .container {
      -ms-flex-wrap: nowrap;
      flex-wrap: nowrap;
      .dropdown-mobile {
        display: none;
      }
      .sticky {
        display: block;
      }
    }
  }
}

// --------- ANIMATION ---------
.learn {
  // Safari 4.0 - 8.0
  @-webkit-keyframes slide-right {
    from {
      transform: translateX(-100%);
      -webkit-transform: translateX(-100%);
    }
    to {
      transform: translateX(0);
      -webkit-transform: translateX(0);
    }
  }
  // Standard syntax
  @keyframes slide-right {
    from {
      transform: translateX(-100%);
      -webkit-transform: translateX(-100%);
    }
    to {
      transform: translateX(0);
      -webkit-transform: translateX(0);
    }
  }

  // Vuejs transition
  .fade-enter-active,
  .fade-leave-active {
    -webkit-transition: opacity 1s ease;
    transition: opacity 1s ease;
  }
  // Vuejs transition
  .fade-delay-enter-active,
  .fade-delay-leave-active {
    -webkit-transition: opacity 1s ease .4s;
    transition: opacity 1s ease .4s;
  }
  .fade-enter, .fade-leave-to,
  .fade-delay-enter, .fade-delay-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
}

// --------- EDITOR CSS ---------
/* wwManager:start */
.learn {
  .ww-orange-button {
    position: absolute;
    top: 0;
    left: 0;
    -webkit-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    z-index: 2;
  }
}
/* wwManager:end */
</style>
