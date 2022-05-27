<template>
  <label class="label-text" :class="addClass">
    <input :value="value" type="text" :name="name" @input="onInput">
    <span>{{ label }}</span>
  </label>
</template>

<script>

export default {
  name: 'AppInput',
  props: {
    label: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true
    },
    value: {
      type: String,
      required: true
    },
    error: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    addClass () {
      const className = []
      this.error ? className.push('error') : className.filter(function (f) { return f !== 'error' })
      this.value === '' ? className.filter(function (f) { return f !== 'notEmpty' }) : className.push('notEmpty')
      return className
    }
  },
  methods: {
    onInput (event) {
      this.$emit('input', event.target.value)
    }
  }
}
</script>

<style scoped lang="scss">
.label-text {
  display: flex;
  flex-direction: column;
  position: relative;
  &.notEmpty span {
    color: #2f2f2f;
    transform: translate(-18px, -40px);
    font-size: 12px;
  }
  &.error:after {
    content: '';
    position: absolute;
    width: 20px;
    height: 20px;
    top: 50%;
    right: 10px;
    transform: translateY(-50%);
    background: red;
  }
  input {
    border: 1px solid #ccc;
    width: 100%;
    height: 50px;
    background: transparent;
    outline: none;
    z-index: 2;
    font-size: 16px;
    color: #313133;
    padding: 18px;
    &:focus {
      border: 1px solid #e35b29;
      color: #313133;
      & + span {
        color: #2f2f2f;
        transform: translate(-18px, -40px);
        font-size: 12px;
      }
    }
    :after {
      content: '111';
    }
  }
  span {
    font-size: 16px;
    font-weight: 400;
    line-height: 50px;
    padding: 0 18px;
    text-align: left;
    color: #7a7a7a;
    position: absolute;
    transition: .3s cubic-bezier(0.6, -0.28, 0.735, 0.045);
  }
}
</style>
