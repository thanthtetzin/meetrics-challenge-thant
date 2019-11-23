<template>
  <div>
    <b-card class="page-visibility-card" title="Card title" sub-title="Card subtitle" no-body>
      <div class="user-status">
        <span
          :class="activeUser ? 'color-green' : 'color-red'"
        >
          {{activeUser ? 'Active' : 'Inactive'}} User
        </span>
      </div>
      <b-table 
        striped 
        hover 
        :items="tableItems"
        :fields="tableFields"
        thead-class="hidden_header"
      >
      </b-table>
    </b-card>
  </div>
</template>

<script>
export default {
  name: 'PageVisibility',
  props: {
    msg: String,
    ads: Array
  },
  data(){
    return{
      activeUser: true,
      advertisements: this.ads,
      tableItems: [],
      tableFields: [],
    }
  },
  mounted(){
    // Register window get focus and lose focus events
    this.$nextTick(function() {
      window.addEventListener('load', this.checkDocumentFocus);
      document.addEventListener("visibilitychange", this.checkDocumentFocus);
      window.addEventListener('focus', this.checkDocumentFocus);
      window.addEventListener('blur', this.checkDocumentFocus);
      window.addEventListener('scroll', this.processOnScroll);

    });

    // Construct the table and load the ads data
    this.advertisements.forEach((advertisement) => {
      this.tableItems.push({
        id: advertisement.elementId,
        name: advertisement.elementName,
        viewable: false,
        viewableTime: "0s",
        viewablePercentage: "0%",
        greaterThanEqual50Percent: false
      });
      this.tableFields = [
        {
          key: 'name',
          sortable: true
        },
        {
          key: 'viewable',
          sortable: false,
          label: 'Viewable'
        },
        {
          key: 'viewableTime',
          sortable: false,
          label: 'ViewableTime'
        },
        {
          key: 'viewablePercentage',
          label: 'Viewable %',
          sortable: false,
        },
        {
          key: 'greaterThanEqual50Percent',
          label: '≥ 50% visible',
          sortable: false,
        }
      ];
      
    });
  },
  methods:{
    checkDocumentFocus(){
      let that = this;
      that.activeUser = document.hasFocus() ? true : false;
      console.log("documentIsFocused: " , 
                  document.hasFocus(), 
                  document.hasFocus() ? ', Active User' : ', Inactive User');
    },
    processOnScroll(){
      console.log('Scroll happens');
      //let that = this;
    }
  }

}





//
// https://jsfiddle.net/hr77p7qb/3/
// var app = new Vue({
//   el: '#app',
//   data: function() {
//     return {
//       msg: 'Hello World! This is a Event listener test.',
//       windowWidth: 0,
//       windowHeight: 0,
//     }
//   },

//   mounted() {
//     this.$nextTick(function() {
//       window.addEventListener('resize', this.getWindowWidth);
//       window.addEventListener('resize', this.getWindowHeight);

//       //Init
//       this.getWindowWidth()
//       this.getWindowHeight()
//     })

//   },

//   methods: {
//     getWindowWidth(event) {
//         this.windowWidth = document.documentElement.clientWidth;
//       },

//       getWindowHeight(event) {
//         this.windowHeight = document.documentElement.clientHeight;
//       }
//   },
//   beforeDestroy() {
//     window.removeEventListener('resize', this.getWindowWidth);
//     window.removeEventListener('resize', this.getWindowHeight);
//   }
// });

//
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.page-visibility-card{
  position: fixed !important;
  right: 25%;
  width: 40%;
  overflow-y: auto;
  font-size: 13px;
  max-height: 500px;
  height: 300px;
  top: 23%;
  box-shadow: 3px 3px 5px 0px rgba(0, 0, 0, 0.5);
}
.user-status > span{
  width: 100px;
  color: white;
  margin: 10px;
  float: right;
}
.color-red{
  background-color: red;
}
.color-green{
  background-color: green;
}
.user-status > span.color-red{
  content: "Inactive User";
}
.user-status > span.color-green{
  content: "Active User";
}
</style>
