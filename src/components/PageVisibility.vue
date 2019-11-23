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
    ad_div_list: Array
  },
  data(){
    return{
      activeUser: true,
      // ad_div_list: this.ad_div_list,
      tableItems: [],
      tableFields: [],
      elementObservers: [],
    }
  },
  mounted(){
    this.registerEvents();
    this.constructAndLoadAdDivListDataToTable();
  },
  methods:{
    /**
     * Register events to window and document.
     */
    registerEvents(){
      let that = this;
      that.$nextTick(function() {
        window.addEventListener('load', that.checkDocumentFocus);
        document.addEventListener("visibilitychange", that.checkDocumentFocus);
        window.addEventListener('focus', that.checkDocumentFocus);
        window.addEventListener('blur', that.checkDocumentFocus);
        //window.addEventListener('scroll', that.processViewabilityOnScroll);
      });
    },
    /**
     * Construct the table and load the ad_div_list data.
     */
    constructAndLoadAdDivListDataToTable(){
      let that = this;
      that.$nextTick(function() {
        let observerOptions = {
          root: null,
          threshold: []
        };
        // Put 0 to 1.0 values to threshold array in order to show 0% to 100% by 1 increment.
        for (let i=0; i<=1.0; i+= 0.01) {
          console.log(parseFloat(i.toFixed(2)));
          observerOptions.threshold.push(parseFloat(i.toFixed(2)));
        }
        that.ad_div_list.forEach((ad_div) => {
          that.tableItems.push({
            id: ad_div.elementId,
            name: ad_div.elementName,
            viewable: false,
            viewableTime: "0s",
            viewablePercentage: "0%",
            greaterThanEqual50Percent: false
          });

          // Register element observer to observe element visibility
          const elementToObserve = document.querySelector("#"+ad_div.elementId);
          if(elementToObserve != null && elementToObserve != undefined){            
            const observer = new IntersectionObserver(that.updateElementViewabilityValues, observerOptions);
            observer.observe(elementToObserve);
            that.elementObservers.push(observer);
          } 
        });

        // Edit Table column names
        that.tableFields = [
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
    /**
     * Update element's viewability properties values.
     */
    updateElementViewabilityValues(entries) {
      let that = this;
      entries.forEach((entry) => {
        let elmentInTable = that.tableItems.find(item => item.id == entry.target.id);
        if(elmentInTable){
          let elementViewablePercentage = (Math.floor(entry.intersectionRatio * 100));
          elmentInTable.viewable = (entry.intersectionRatio > 0);
          elmentInTable.viewablePercentage = elementViewablePercentage + "%";
          elmentInTable.greaterThanEqual50Percent = (elementViewablePercentage >= 50);
        }
      });
    },
    /**
     * Check if document has focus or not.
     */
    checkDocumentFocus(){
      let that = this;
      that.activeUser = document.hasFocus() ? true : false;
      console.log("documentIsFocused: " , 
                  document.hasFocus(), 
                  document.hasFocus() ? ', Active User' : ', Inactive User');
    },
    /**
     * Do the elements viewability process when browser window get scrolling.
     */
    processViewabilityOnScroll(){
      console.log('Scroll happens');
      let that = this;
      //console.log(this.$parent.$refs.ad.getBoundingClientRect());

      var bounding = that.$parent.$refs.ad.getBoundingClientRect();
      console.log('el_top: ' + bounding.top + ' >= 0');
      console.log('el_left: ' + bounding.left + ' >= 0');
      console.log('el_bottom: ' + bounding.bottom + ' <= ' + (window.innerHeight || document.documentElement.clientHeight) + " (windowHeight)");
      console.log('el_right: ' + bounding.right + ' <= ' + (window.innerWidth || document.documentElement.clientWidth) + " (windowWidth)");

      // that.tableItems.forEach(() => {
      //   let ad_element = this.$refs.ad;
      //   console.log(ad_element)
      //   // if(ad_element.length){
      //   //   console.log(ad_element.getBoundingClientRect());
      //   // }
       
      // });
    },
    isInViewport(elem) {
      var bounding = elem.getBoundingClientRect();
      return (
          bounding.top >= 0 &&
          bounding.left >= 0 &&
          bounding.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
          bounding.right <= (window.innerWidth || document.documentElement.clientWidth)
      );
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
