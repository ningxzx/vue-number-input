<template>
    <div data-index="number-input">
    </div>
</template>
<script>
  import keyboard from './keyboard'
  import validateDataType from './data-types'

  export default {
    name:'numInput',
    data() {
      return {
      inputType:'int',
      showKeyboard: false,
      }
    },
    mounted:function(){
      document.body.addEventListener('click', this.handleFocusOrBlur)
    },
    methods:{
      handleFocusOrBlur:function(e) {
      let numberInputIndex=e.target.getAttribute('data-index')
      let valueIndex = e.target.getAttribute('value-index')

      // 当前输入框以及键盘按钮都附加上了numberInputIndex
      // 当这些对象被点击的时候，会让当前输入框保持focus状态
      if (numberInputIndex==0) {
          // 确保focus会在可能的blur之后执行
          setTimeout(()=> {
              this.handleFocus()
          } , 0) 
          if (valueIndex >=0) {
              // 点击输入框内部的字符元素，会得到该字符的valueIndex
              // 然后根据valueIndex来更改光标的位置
              this.cursorPosition=valueIndex - 1
          }
          else {
              // 如果没有获取到，则将光标移到最后
              this.cursorPosition=null
          }
      }
      else {
          this.handleBlur()
      }
},
showNum:function(val){
  console.log(val)
},
 handleFocus:function() {
  console.log('focus');
    // 滑出键盘组件，传递必要状态
    this.showKeyboard=true;
    this.focus= true;

    // 按需触发onFocus回调
    this.onFocus && this.onFocus()

  },
  handleBlur:function() {
      console.log('blur');
    // 隐藏键盘组件
   this.showKeyboard=false;

    this.focus=false

    // 按需触发onBlur回调
    this.onBlur && this.onBlur();

  },

  focus:function() {

    this.handleFocus()

  },

  blur:function() {

    this.handleBlur()

  },

  getValue:function() {

    // 通过组件实例方法来获取当前的值
    return this.value

  },

  setValue:function(value) {

    // 通过组件实例方法来设置值
    this.handleChange(value)

  },

  clear:function() {

    // 通过组件实例方法来清空值
    this.value='';

  },

  disable:function() {

    // 通过组件实例方法来禁用组件
    this.disabled=true;

  },

  enable:function() {

    // 通过组件实例方法来启用组件
    this. disabled= false;

  },

  // 计算插入操作之后的值
  valueAfterInsert:function(newValue) {

    let { value, cursorPosition } = this.state

    // 根据当前光标位置执行插入操作
    if (cursorPosition === null) {
      return value + newValue
    } else {
      return value.slice(0, cursorPosition + 1) + newValue + value.slice(cursorPosition + 1)
    }

  },

  // 计算删除操作之后的值
  valueAfterDelete:function() {

    let { value, cursorPosition } = this.state
    let valueArray = value.split('')

    // 根据当前光标位置执行删除操作
    if (cursorPosition === null) {
      return value.slice(0, -1)
    } else {
      valueArray.splice(cursorPosition, 1)
      return valueArray.join('')
    }
  },
   handleChange:function(newValue) {

    let { value, cursorPosition } = this.state
    let { type, matchReg, maxLength, max, decimal } = this.props
    let applyChange = true
    let matched = false

    if (newValue !== 'delete') { //非删除操作

      value = this.valueAfterInsert(newValue)
      cursorPosition !== null && (cursorPosition = cursorPosition + 1)

      if (value[0] === '.') {
        value = '0' + value
        cursorPosition !== null && (cursorPosition = cursorPosition + 1)
      }

    } else { // 删除操作

      // 光标位置在最前的时候不执行操作
      if (cursorPosition === -1) {
        return false
      }

      // 获取删除后的数据
      value = this.valueAfterDelete()

      // 如果光标不是在最后，则将光标位置前移
      cursorPosition !== null && (cursorPosition = cursorPosition - 1)

    }

    // 根据输入框类型来验证用户输入行为是否有效
    if (!validateDataType(type, value, decimal)) {
      applyChange = false
      value = this.state.value
      cursorPosition = this.state.cursorPosition
    }

    // 根据指定的数据最大长度来验证用户输入行为是否有效
    if (!isNaN(maxLength) && value.length > maxLength) {
      applyChange = false
      value = this.state.value
    }

    // 根据指定的数据最大值来验证用户输入行为是否有效
    if (!isNaN(max) && value * 1 > max) {
      applyChange = false
      value = this.state.value
    }

    // 如果指定了验证正则表达式
    if (Object.prototype.toString.call(matchReg) === "[object RegExp]") {

      if (matchReg.test(this.state.value)) {

        // 当前数据匹配指定的正则表达式，则执行onMatch回调
        matched = true
        this.props.onMatch && this.props.onMatch(this.state.value)

      } else {

        // 否则执行onNotMatch回调
        matched = false
        this.props.onNotMatch && this.props.onNotMatch(this.state.value)

      }

    }

    // 按需执行用户传入的onChange回调，如果onChange回调返回false，则用户此次的输入行为不生效
    if (typeof this.props.onChange === 'function') {
      applyChange = this.props.onChange(value, newValue)
    }

    // 非删除操作或者输入行为有效，才应用此次输入，同时更改光标位置和正则匹配结果
    (applyChange !== false || newValue === 'delete') && this.setState({ value, cursorPosition, matched })

  }

    },
    components:{
      keyboard
    },
    beforeDestroy:function(){

    this.mounted = false

    // 文本框组件卸载之前，调用handleBlur来隐藏键盘组件
    this.handleBlur()

    // 清除定时器
    clearTimeout(this.timer)

    // 移除事件监听器
    document.body.removeEventListener('click', this.handleFocusOrBlur.bind(this), false)
    }
  }
</script>
<style>
  @import './index.css'
</style>