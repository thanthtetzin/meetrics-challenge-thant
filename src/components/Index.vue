<template>
  <div>    
    <div id="ad" ref="ad">
      ADVERTISEMENT
    </div>
    <div id="ad2" ref="ad2" style="margin-top:30px; background-color: #2ea5ea;">
      ADVERTISEMENT 2
    </div>
    <PageVisibility 
      v-bind:ad_div_list="ad_div_list"
    />
  </div>
 
</template>

<script>
import PageVisibility from './PageVisibility.vue';

export default {
  name: 'Index',
  components: {
    PageVisibility
  },
  data() {
    return {
      ad_div_list:[
        {elementRef: "ad", elementName: "ADVERTISEMENT"},
        {elementRef: "ad2", elementName: "ADVERTISEMENT 2"},
      ],
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
  height: 2000px;
  width: 2500px;
}
#ad, #ad2 {
  width: 300px;
  height: 250px;
  background: #ee7202;
  position: relative;
  left: 100px;
  top: 100px;
}
</style>
