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
        <!-- overriding the fields to show how we want -->
        <template v-slot:cell(viewedTime)="data">
          {{ data.item.viewedTime }}s
        </template>
        <template v-slot:cell(viewablePercentage)="data">
          {{ data.item.viewablePercentage }}%
        </template>
      </b-table>
    </b-card>
  </div>
</template>

<script>
export default {
  name: 'ViewabilityDetector',
  props: {
    msg: String,
    ad_div_list: Array
  },
  data(){
    return{
      activeUser: true,
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
        for (let i=0; i<=1; i+= 0.01) {
          observerOptions.threshold.push(parseFloat(i.toFixed(2)));
        }
        observerOptions.threshold.push(1.0);

        // Loop ad element that we got from parent Component Index.vue.
        that.ad_div_list.forEach((ad_div) => {
          const elementToObserve = that.$parent.$refs[ad_div.elementRef];
          if(elementToObserve != null && elementToObserve != undefined){   
            that.tableItems.push({
              id: ad_div.elementRef,
              name: ad_div.elementName,
              viewable: false,
              viewedTime: 0,
              viewedTimeCounter: null,
              viewablePercentage: "0",
              greaterThanEqual50Percent: false,
              clicks: 0
            });

            // Register element observer to observe element visibility
            const observer = new IntersectionObserver(that.updateElementViewabilityValues, observerOptions);
            observer.observe(elementToObserve);
            that.elementObservers.push(observer);

            // Register onclick on element
            elementToObserve.addEventListener('click', (() => {
              let elementRef = ad_div.elementRef;
              return () => { 
                that.trackElementOnClick(elementRef);
              }
            })());
            
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
            key: 'viewedTime',
            sortable: false,
            label: 'ViewedTime'
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
          },
          {
            key: 'clicks',
            label: 'Clicks',
            sortable: false,
          },
        ];
      });
    },
    /**
     * Track the clicks on element.
     */
    trackElementOnClick(elementRef){
      let that = this;
      let clickedElement = that.tableItems.find(item => item.id == elementRef);
      if(clickedElement != null && clickedElement != undefined){
        clickedElement.clicks++;
      }
    },
    /**
     * Update element's viewability properties values.
     */
    updateElementViewabilityValues(entries) {
      let that = this;
      if(that.activeUser){
        entries.forEach((entry) => {
          let elmentInTable = that.tableItems.find(item => item.id == entry.target.id);
          if(elmentInTable){
            let elementViewablePercentage = Math.floor(entry.intersectionRatio * 100);
            elmentInTable.viewable = (entry.intersectionRatio > 0);
            elmentInTable.viewablePercentage = elementViewablePercentage;
            elmentInTable.greaterThanEqual50Percent = (elementViewablePercentage >= 50);

            // Start viewedTimeCounter of viewing in seconds if the element viweable percentage is >= 50
            if(elmentInTable.greaterThanEqual50Percent && elmentInTable.viewable){
              if(elmentInTable.viewedTimeCounter == null){
                elmentInTable.viewedTimeCounter = setInterval(function() {
                  let viewedTime = Number(elmentInTable.viewedTime);
                  viewedTime+=0.1;
                  elmentInTable.viewedTime = viewedTime.toFixed(1);
                }, 500);
              } 
            } else {
              clearInterval(elmentInTable.viewedTimeCounter);
              elmentInTable.viewedTimeCounter = null;
            }
          }
        });
      }
    },
    /**
     * Check if document has focus or not. 
     * If has focus, it is activeUser. If lost focus, it is inactive user.
     * If inactive user, stop the viewedTime timer. If active user, continue playing the timer.
     */
    checkDocumentFocus(){
      let that = this;
      that.activeUser = document.hasFocus() ? true : false;
      console.log("documentIsFocused: " , 
                  document.hasFocus(), 
                  document.hasFocus() ? ', Active User' : ', Inactive User');

      that.controlElementViewedTimeCounterStartStop();
    },
    /**
     * Control element's viewedTimeCounter stop, start based on active/Inactive user.
     */
    controlElementViewedTimeCounterStartStop(){
      let that = this;
      if(!that.activeUser){
        let elementsThatHasRunningCounter = that.tableItems.filter(item => item.viewedTimeCounter != null);
        elementsThatHasRunningCounter.forEach((element) => {
          clearInterval(element.viewedTimeCounter);
          element.viewedTimeCounter = null;
        });
      }else{
        let viewableElements = that.tableItems.filter(item => item.greaterThanEqual50Percent && item.viewable);
        viewableElements.forEach((element) => {
          if(element.viewedTimeCounter == null){
              element.viewedTimeCounter = setInterval(function() {
                let viewedTime = Number(element.viewedTime);
                viewedTime+=0.1;
                element.viewedTime = viewedTime.toFixed(1);
              }, 500);
            }
        });
      }
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
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.page-visibility-card{
  position: fixed !important;
  right: 23%;
  width: 42%;
  overflow-y: auto;
  font-size: 13px;
  max-height: 338px;
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
