<template>
  <input
    placeholder="SEARCH"
    class="input"
    :value="value"
    v-on="{
    ...$listeners,
    input: event => $emit('input', event.target.value)
  }"
  >
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: '',
    }
  }
}
</script>

<style lang="scss" scoped>

@import '@/variables.scss';

.input {
  padding: 10vh 1.5rem;
  font-weight:bold;
  border: 0;
  background:transparent;
  border-radius:0;
  color: $white;
  text-align:center;
  font-size: 2.5rem;
  color:$white;
  width: 80%;
}
</style>

<style lang="scss">

@import '@/variables.scss';

.input {
  &::placeholder{
    opacity:0.3;
    color:$secondary-light;
    .night &{
      color:$primary-light;
    }
  }
  &:focus{
    outline:0;
    &::placeholder{
      color:transparent;
    }
  }
}
</style>
