<template>
  <v-layout>
    <v-flex text-xs-center>
      <div>
        <PreferencesDropdown
                :themes="themes"
                :current-theme.sync="currentTheme"
                :font-size.sync="size"
                v-model="searchQuery"
                @searchResults="onSearchResults"
        >
          <template slot="book-content" slot-scope="props">
            <button class="my-find my-content" @click="showContent">
              <img src="https://uploads.codesandbox.io/uploads/user/8973517b-ade3-4128-b0c2-2cf0f387882a/4_gy-icons8-align-left-50.png" alt="">
            </button>
            <div class="search-widget1" v-if="openContent">
              <TreeMenu :subContent="toc"/>
            </div>
          </template>
          <template slot="book-search" slot-scope="props">
            <button class="my-find" @click="toggleSearchWidget">
              <img id="find" src="https://uploads.codesandbox.io/uploads/user/8973517b-ade3-4128-b0c2-2cf0f387882a/Ciwo-icons8-search-50.png" alt="">
            </button>
            <div class="search-widget1" v-if="openSearch">
              <input type="text"
                     :value="searchQuery"
                     @change="props.findText($event.target.value)"
              >
              <button @click="props.removeHighlight">x</button>
              <ul>
                <li v-for="(excerpt, i) in searchContent" :key="i" @click="props.showPage(excerpt.cfi)">
                  <p>{{excerpt.excerpt}}</p>
                </li>
              </ul>
            </div>
          </template>
        </PreferencesDropdown>
        <BookReader
                :epub-url="url"
                :font-size="size"
                :themes="themes"
                :theme="currentTheme"
                :progress.sync="readingProgress"
                :book-area="'area-content'"
                @toc="getContent"
                :contentBookModify="140"
        >
          <template slot="book-content" slot-scope="props">
            <div id="area-content" class="content-container"></div>
          </template>
          <template slot="progress-bar" slot-scope="props">
            <div class="progress-bar">
              <input size="3" type="range" max="100" min="0" step="1" id="input-progress-bar"
                     @change="props.onChange($event.target.value)"
                     :value="readingProgress"
              /> %
              <input type="text" id="input-progress-bar-value"
                     @change="props.onChange($event.target.value)"
                     :value="readingProgress"
              >
              <!--@mousedown="props.onMousedown"-->
              <!--@mouseup="props.onMouseup"-->
            </div>
          </template>
        </BookReader>
      </div>

    </v-flex>
  </v-layout>
</template>

<script>

  import  BookReader from "../components/vue-epub-reader/book-reader/BookReader.vue";
  import  PreferencesDropdown from "../components/vue-epub-reader/preferences-dropdown/";
  import  TreeMenu from "../components/vue-epub-reader/tree-menu/";
  export default {
    name: "App",
    components: {
      BookReader,
      PreferencesDropdown,
      TreeMenu
    },
    data() {
      return {
        url: "https://s3-eu-west-1.amazonaws.com/react-reader/alice.epub",
        size: 70,
        currentTheme: "night",
        themes: {
          white: {
            body: {
              color: "#000000",
              background: "#ffffff"
            },
            name: "WHITE"
          },
          beige: {
            body: {
              color: "#000000",
              background: "#f3e8d2"
            },
            name: "BEIGE"
          },
          night: {
            body: {
              color: "#ffffff",
              background: "#4a4a4a"
            },
            name: "NIGHT"
          }
        },
        searchQuery: "",
        readingProgress: 20,
        openSearch: false,
        openContent: false,
        searchContent: [],
        toc: []
      };
    },
    methods: {
      toggleSearchWidget() {
        this.openSearch = !this.openSearch;
      },

      showContent() {
        this.openContent = !this.openContent;
      },

      onSearchResults(value) {
        this.searchContent = value;
      },

      getContent(value) {
        this.toc = value;
      }
    },
    mounted() {
      this.$root.$on("toc", toc => {
        this.toc = toc;
      });
    }
  };
</script>

<style>
  html,
  body {
    margin: 0;
    width: 100%;
    height: 100%;
  }

  .search-widget1 {
    overflow: auto;
    position: fixed;
    padding: 20px;
    border: 5px solid #f1f1f1;
    background: #fff;
    border-radius: 5px;
    font-family: "Roboto", sans-serif;
    width: 16%;
    height: 81%;
    z-index: 100;
  }

  .search-widget1 h3 {
    margin-bottom: 20px;
    text-align: center;
    font-size: 24px;
    font-weight: normal;
    color: #424949;
  }

  .search-widget1 ul {
    margin: 0;
    padding: 0;
    list-style: none;
    width: 250px;
  }

  .search-widget1 li {
    border-bottom: 1px solid #eaeaea;
    padding-bottom: 15px;
    margin-bottom: 15px;
  }

  .search-widget1 li:before {
    font-family: FontAwesome;
    font-size: 20px;
    vertical-align: bottom;
    color: #dd3333;
    margin-right: 14px;
  }

  #area-content {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 65%;
    margin-top: 70px;
  }

  #area-content iframe {
    border: none;
  }

  .progress-bar {
    margin-top: 78vh;
  }

  #input-progress-bar {
    width: 95%;
  }

  #input-progress-bar-value {
    width: 2%;
  }

  #find {
    width: 20px;
  }

  .buton {
    background-color: #4a4a4a;
    border: none;
    color: white;
    padding: 13px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 22px;
    margin: 26px -115px;
    border-radius: 50%;
    font-family: 'Montserrat', sans-serif;
  }
</style>
