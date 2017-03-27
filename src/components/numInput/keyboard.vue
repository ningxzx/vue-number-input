<template>
        <div class="number-input-keyboard active">
        <button v-for="num in numArr" 
        :data-value="num" 
        :class="{disabled:initClass(num)}"
        v-on:touchstart="handleInput"
        >
          {{showText(num)}}
        </button>
        <button v-on:touchstart="deleteStart" v-on:touchend="deleteEnd" data-value="delete"></button>
      </div>
</template>
<script>
  export default {
      name:'keyboard',
      props: {
          'inputType': {
              type: String, default: 'ID'
          }
      }
      ,
      data () {
          return {
              numArr: [1, 2, 3, 4, 5, 6, 7, 8, 9, 'blank', 0], inputFieldIndex: null, deleteTimer: null, deleteDelayTimer: null, stateUpdateTimer: null
          }
      }
      ,
      mounted:function() {
          clearTimeout(this.deleteTimer);
          clearTimeout(this.deleteDelayTimer);
          document.body.addEventListener('touchstart', function(e) {
              e.preventDefault();
          }
          , false);
      }
      ,
      beforeDestroy:function() {
          clearTimeout(this.deleteTimer);
          clearTimeout(this.deleteDelayTimer);
          clearTimeout(this.stateUpdateTimer);
          document.body.removeEventListener('touchstart', function(e) {
              e.preventDefault();
          }
          , false);
      }
      ,
      methods: {
          showText:function(num) {
              if(num=='blank') {
                  return this.inputType==="ID"?'X': (this.inputType==="int"?"": '.');
              }
              return num;
          }
          ,
          initClass:function(num) {
              if(num==='blank'&&this.inputType==="int") {
                  return true;
              }
              return false;
          }
          ,
          handleInput:function(event) {
              let value=event.target.getAttribute('data-value');
              this.$emit('chooseNum', value)
          }
          ,
          deleteStart:function() {
              this.$emit('chooseNum', 'delete');
              this.deleteDelayTimer=setTimeout(()=> {
                  this.deleteTimer=setInterval(()=> {
                      this.$emit('chooseNum', 'delete')
                  }
                  , 100)
              }
              , 800)
          }
          ,
          deleteEnd:function() {
              clearTimeout(this.deleteTimer);
              clearTimeout(this.deleteDelayTimer);
          }
      }
  }

</script>,
<style scoped>
  @import './keyboard.css'
</style>

