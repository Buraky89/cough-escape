<template>
  <div class="player" :class="getHasCoughingEffect" :key="id" :style="getPlayerStyle">{{ getPlayerShortName }}</div>
</template>

<script>
export default {
  name: 'Player',
  props: ['player', 'time', 'forcedX', 'forcedY', 'id'],
  data: function(){
    var p = this.initiatePlayer();
    console.log(p);
    return {
      name: p.isMe ? "You" : p.name,
      x: p.x,
      y: p.y,
      autoX: p.autoX,
      autoY: p.autoY,
      isMe: p.isMe,
      isCoughing: false,
      coughProbabilityPer100000: p.coughProbabilityPer100000,
      coughingPeriod: {
        startTime: null,
        endTime: null
      }
    };
  },
  watch: { 
    time: function() { // watch it
      this.setProbableCoughing();
    }
  },
  computed: {
    getPlayerStyle() {
      // eslint-disable-next-line no-unused-vars
      var zeit = this.time;

      var ourMan = this;


      var targetX = this.forcedX ? this.forcedX : this.autoX;
      var targetY = this.forcedY ? this.forcedY : this.autoY;

      var yMovementDirection = Math.sign(targetY - ourMan.y);
      var xMovementDirection = Math.sign(targetX - ourMan.x);
      var speed = 1;

      var alpha = Math.atan(Math.abs((targetX - ourMan.x) / (targetY - ourMan.y)));
      var yDifference = speed * yMovementDirection * Math.cos(alpha);
      var xDifference = speed * xMovementDirection * Math.sin(alpha);

      ourMan.x = ourMan.x + xDifference;
      ourMan.y = ourMan.y + yDifference;

      var howClose = Math.sqrt(Math.pow(ourMan.x - targetX, 2) + Math.pow(ourMan.y - targetY, 2));
      var playerIsCloseEnoughToTarget = howClose < 4;

      if(playerIsCloseEnoughToTarget && !ourMan.isMe){
        var nextLocation = this.decideNextLocation(ourMan.x, ourMan.y);
        var autoX = nextLocation.x;
        var autoY = nextLocation.y;

        ourMan.autoX = autoX;
        ourMan.autoY = autoY;
      }

      return "position: absolute; top: " + this.getReadableLocationUnit(this.y.toString()) + "px; left: " + this.getReadableLocationUnit(this.x.toString()) + "px; background-color: " + /*this.getPlayerBackgroundColor(this.name)*/ this.getPlayerBackgroundColorByProbability(this.coughProbabilityPer100000) + "; color: " + this.pickTextColorBasedOnBgColorSimple(this.getPlayerBackgroundColor(this.name)) + ";";
    },
    getHasCoughingEffect() {
      if(this.isCoughing){
        return "anim";
      }
      return "";
    },
    getPlayerShortName() {
      return this.name.substring(0, 3);
    }
  },
  methods: {
    setProbableCoughing(){
      if(!this.isCoughing){
        var randomInt = this.getRandomInt(100000);
        var coughProb = this.coughProbabilityPer100000;
        var willCough = randomInt <= (coughProb);
        if(willCough){
          this.isCoughing = true;
          this.coughingPeriod = {
            startTime: this.time,
            endTime: this.time + 300
          };
          this.$emit('cough', {"id": this.id, "x": this.x, "y": this.y, "period": this.coughingPeriod });
        } else {
        }
      } else {
        if(this.coughingPeriod.endTime < this.time){
          this.isCoughing = false;
          this.coughingPeriod = {
            startTime: null,
            endTime: null
          };
        }
      }
    },
    initiatePlayer() {
      var randomName = this.$faker().name.firstName();
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      var nextLocation = this.decideNextLocation(x, y);
      var autoX = nextLocation.x;
      var autoY = nextLocation.y;

      var coughProbabilityPer100000 = this.getRandomInt(10);
      if(this.id == -1) coughProbabilityPer100000 = 0;

      return {"name": randomName, "x": x, "y": y, "autoX": autoX, "autoY": autoY, "isMe": this.id == -1, "coughProbabilityPer100000": coughProbabilityPer100000};
    },
    decideNextLocation(x, y){
      var newPoint = this.generateRandomPoint(x, y, 100);
      while(newPoint.x < 0 || newPoint.x >= 500 ||newPoint.y < 0 || newPoint.y >= 500){
        newPoint = this.generateRandomPoint(x, y, 100);
      }
      return {x: newPoint.x, y: newPoint.y};
    },
    generateRandomPoint(x, y, distance) {
        var alpha = this.getRandomInt(360);
        return {"x": (x + distance * Math.cos(alpha)), "y": (y + distance * Math.sin(alpha))}
    },
    getRandomInt(max) {
      return Math.floor(Math.random() * max);
    },
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
      if(this.isMe) return "#ffffff";
      return "#" + this.intToRGB(this.hashCode(str));
    },
    getPlayerBackgroundColorByProbability(degree){
      return "#ff" + (255 - 20 * degree).toString(16) + (255 - 20 * degree).toString(16);
    },
    /* eslint-disable */
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
    /* eslint-enable */
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

.player.anim {
  background-clip: content-box;
  animation: spin 10s linear infinite;
  border: 4.5px dashed #CA0B00;
}

@keyframes spin { 
  100% { 
    transform: rotateZ(360deg);
  }
}


</style>
