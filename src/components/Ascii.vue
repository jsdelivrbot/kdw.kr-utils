<template>
  <div id="ascii">
    <center><h2>아스키 코드 표</h2></center>
    <div class="list">
      <div :class="$style['item']" v-for="obj in list">
        <div :class="$style['ascii']">{{obj.ascii}}</div>
        <div :class="$style['char']">{{obj.char}}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      list: []
    }
  },
  mounted () {
    for(var i=1;i<=1000;i++)this.list.push({ascii: i, char: String.fromCharCode(i)})
  },
  watch: {
  }
}
</script>

<style lang="scss" module>
.item {
  display: inline-block;
  margin: 0.1rem;
}
.ascii {
  border-left: 1px solid #ccc;
  border-right: 1px solid #ccc;
}
.char {
  font-family: arial,sans-serif;
}
.ascii, .char {
  padding: 0.2rem;
  width: 2rem;
  text-align: center;
  display: inline-block;
}
</style>
<style lang="scss" scoped>
</style>
