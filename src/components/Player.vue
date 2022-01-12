<template>
  <div class="player" :key="player.id" :style="getPlayerStyle">{{ getPlayerShortName }}</div>
</template>

<script>
export default {
  name: 'Player',
  props: ['player'],
  computed: {
    getPlayerStyle() {
      return "position: absolute; top: " + this.getReadableLocationUnit(this.player.y.toString()) + "px; left: " + this.getReadableLocationUnit(this.player.x.toString()) + "px; background-color: " + this.getPlayerBackgroundColor(this.player.name) + "; color: " + this.pickTextColorBasedOnBgColorSimple(this.getPlayerBackgroundColor(this.player.name)) + ";";
    },
    
    getPlayerShortName() {
      return this.player.name.substring(0, 3);
    }
  },
  methods: {
    getReadableLocationUnit(u){
      if(u.toString().indexOf(".") > 0){
        return u.toString().substring(0, u.indexOf("."));
      } else {
        return u;
      }
      // TODO: cover weird types etc
    },
    hashCode(str) {
        var hash = 0;
        for (var i = 0; i < str.length; i++) {
          hash = str.charCodeAt(i) + ((hash << 5) - hash);
        }
        return hash;
    },
    intToRGB(i){
      var c = (i & 0x00FFFFFF)
          .toString(16)
          .toUpperCase();

      return "00000".substring(0, 6 - c.length) + c;
    },
    getPlayerBackgroundColor(str) {
      return "#" + this.intToRGB(this.hashCode(str));
    },
    pickTextColorBasedOnBgColorSimple(bgColor) {
      var lightColor = '#FFFFFF';
      var darkColor = '#000000';
      var color = (bgColor.charAt(0) === '#') ? bgColor.substring(1, 7) : bgColor;
      var r = parseInt(color.substring(0, 2), 16); // hexToR
      var g = parseInt(color.substring(2, 4), 16); // hexToG
      var b = parseInt(color.substring(4, 6), 16); // hexToB
      return (((r * 0.299) + (g * 0.587) + (b * 0.114)) > 186) ?
        darkColor : lightColor;
    }
  }
}
</script>

<style scoped>
.player {
  width: 30px;
  height: 30px;
  display: inline-block;
  border-radius: 50%;
  line-height: 30px;
  text-align: center;
  border: 1px solid #ccc;
  font-family: Verdana;
  font-size: 11px;
}

</style>
