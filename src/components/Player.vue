<template>
  <div class="player" :class="getAdditionalClasses" :key="id" :style="getPlayerStyle">{{ coughProbabilityPer10000 }}</div>
</template>

<script>
export default {
  name: 'Player',
  props: ['player', 'time', 'forcedX', 'forcedY', 'id', 'coughList'],
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
      coughProbabilityPer10000: p.coughProbabilityPer10000, // TODO: use a float number instead, and change this name to coughProbability.
      coughingPeriod: {
        startTime: null,
        endTime: null
      },
      outsideCoughList: []
    };
  },
  watch: { 
    time: function() { // watch it
      this.makeMovement(),
      this.setProbableCoughing();
      this.processOutsideCoughs();
      this.makePlayerDieIfTooSick();
    },
    coughList: function(newValue){
      this.outsideCoughList.push(newValue[newValue.length - 1]);
    }
  },
  computed: {
    getPlayerStyle() {
      return "position: absolute; top: " + this.getReadableLocationUnit(this.y.toString()) + "px; left: " + this.getReadableLocationUnit(this.x.toString()) + "px; background-color: " + /*this.getPlayerBackgroundColor(this.name)*/ this.getPlayerBackgroundColorByProbability(this.coughProbabilityPer10000) + "; color: " + this.pickTextColorBasedOnBgColorSimple(this.getPlayerBackgroundColorByProbability(this.coughProbabilityPer10000)) + ";";
    },
    getAdditionalClasses(){
      var classNames = []
      if(this.isCoughing){
        classNames.push("anim");
      }
      if(this.id == -1){
        classNames.push("me");
      }
      return classNames.join(" ");
    },
    getPlayerShortName() {
      return this.name.substring(0, 3);
    }
  },
  methods: {
    destroy () {
      // destroy the vue listeners, etc
      this.$destroy();

      // remove the element from the DOM
      this.$el.parentNode.removeChild(this.$el);
    },
    makePlayerDieIfTooSick(){
      if(this.coughProbabilityPer10000 > 25){
        this.$emit("playerDied", this.id)
        if(this.id != -1) {
          Object.assign(this.$data, this.$options.data.call(this));
          this.destroy();
        } else {
          Object.assign(this.$data, this.$options.data.call(this));
        }
      }
    },
    processOutsideCoughs() {
      var unprocessedCoughs = this.outsideCoughList.filter(x => !x.isProcessed && x.id != this.id && x.period.startTime < this.time && this.time < x.period.endTime);
      for(var i = 0; i < unprocessedCoughs.length; i++){
        this.processSingleOutsideCough(unprocessedCoughs[i]);
      }
    },
    processSingleOutsideCough(cough) {
      var howClose = Math.sqrt(Math.pow(this.x - cough.x, 2) + Math.pow(this.y - cough.y, 2));
      if(howClose < 50){
        cough.isProcessed = true;
        this.coughProbabilityPer10000 += cough.pointsToTransfer;
      }
    },
    makeMovement(){
      var targetX = this.forcedX ? this.forcedX : this.autoX;
      var targetY = this.forcedY ? this.forcedY : this.autoY;

      var yMovementDirection = Math.sign(targetY - this.y);
      var xMovementDirection = Math.sign(targetX - this.x);
      var speed = 1;

      var alpha = Math.atan(Math.abs((targetX - this.x) / (targetY - this.y)));
      var yDifference = speed * yMovementDirection * Math.cos(alpha);
      var xDifference = speed * xMovementDirection * Math.sin(alpha);

      var howClose = Math.sqrt(Math.pow(this.x - targetX, 2) + Math.pow(this.y - targetY, 2));
      var playerIsCloseEnoughToTarget = howClose < 4;

      if(playerIsCloseEnoughToTarget && !this.isMe){
        var nextLocation = this.decideNextLocation(this.x, this.y);
        var autoXnew = nextLocation.x;
        var autoYnew = nextLocation.y;

        this.autoX = autoXnew;
        this.autoY = autoYnew;
      } else {
        if(!(this.isMe  && playerIsCloseEnoughToTarget)){
          this.x = this.x + xDifference;
          this.y = this.y + yDifference;
        }
      }
    },
    setProbableCoughing(){
      if(!this.isCoughing){
        var randomInt = this.getRandomInt(10000);
        var coughProb = this.coughProbabilityPer10000;
        var willCough = randomInt < (coughProb);
        if(willCough){
          this.isCoughing = true;
          this.coughingPeriod = {
            startTime: this.time,
            endTime: this.time + 30
          };
          this.$emit('cough', {"id": this.id, "x": this.x, "y": this.y, "period": this.coughingPeriod, "pointsToTransfer": this.coughProbabilityPer10000 });
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
      var x = this.getRandomInt(470);
      var y = this.getRandomInt(470);

      var nextLocation = this.decideNextLocation(x, y);
      var autoX = nextLocation.x;
      var autoY = nextLocation.y;

      var coughProbabilityPer10000 = this.getRandomInt(10);
      if(this.id == -1) coughProbabilityPer10000 = 0;

      return {"name": randomName, "x": x, "y": y, "autoX": autoX, "autoY": autoY, "isMe": this.id == -1, "coughProbabilityPer10000": coughProbabilityPer10000};
    },
    decideNextLocation(x, y){
      var newPoint = this.generateRandomPoint(x, y, 100);
      while(newPoint.x < 0 || newPoint.x >= 470 ||newPoint.y < 0 || newPoint.y >= 470){
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
      //if(this.isMe) return "#ffffff";
      return "#" + this.intToRGB(this.hashCode(str));
    },
    getPlayerBackgroundColorByProbability(degree){
      if(degree > 10) degree = 10;
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

.player.me {
  border: 1px dashed orange;
}

.player.anim {
  background-clip: content-box;
  animation: spin 10s linear infinite;
  border: 4.5px dashed #CA0B00 !important;
}

@keyframes spin { 
  100% { 
    transform: rotateZ(360deg);
  }
}


</style>
