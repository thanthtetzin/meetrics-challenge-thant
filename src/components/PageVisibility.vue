<template>
  <div>
    <b-card class="page-visibility-card" title="Card title" sub-title="Card subtitle" no-body>
      <b-table 
        striped 
        hover 
        :items="tableItems"
        thead-class="hidden_header"
      ></b-table>
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
      advertisements: this.ads,
      tableItems: []
    }
  },
  mounted(){
    this.advertisements.forEach((advertisement) => {
      this.tableItems.push({
        elementId: advertisement.elementId,
        elementName: advertisement.elementName,
        viewdPeriod: "0s",
        viewablePercentage: "0%",
        greaterThanEqual50Percent: false
      })
    });
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.page-visibility-card{
  position: fixed !important;
  right: 25%;
  width: 40%;
  top: 23%;
  box-shadow: 3px 3px 5px 0px rgba(0, 0, 0, 0.5);
}
</style>
