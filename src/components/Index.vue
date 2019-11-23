<template>
  <div>    
    <div id="ad">
      ADVERTISEMENT
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
        {elementId: "ad", elementName: "ADVERTISEMENT"},
        {elementId: "ad2", elementName: "ADVERTISEMENT2"},
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
#ad {
  width: 300px;
  height: 250px;
  background: #ee7202;
  position: relative;
  left: 100px;
  top: 100px;
}
</style>
