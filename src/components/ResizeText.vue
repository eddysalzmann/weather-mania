<template>
  <span>
    <slot></slot>
  </span>
</template>

<script>
export default {
  methods: {
    longest_string(str_ara) {
      var max = str_ara[0].length;
      str_ara.map(v => max = Math.max(max, v.length));
      let result = str_ara.filter(v => v.length == max);
      return result;
    },
    calculate() {

      let element = this.$el.innerHTML,
          element_parts = element.split(" "),
          win_width = document.body.clientWidth,
          element_length = this.longest_string(element_parts)[0].length,
          fontSize = ( ( win_width / 100 )  / element_length) * 10;

      if(fontSize > 16){
        fontSize = 16;
      }
      this.$el.style.fontSize = fontSize + 'vh';
    },

  },

  updated() {

    this.calculate();
    if ('MutationObserver' in window && this.observer === null) {
      this.observer = new MutationObserver(this.calculate);
      this.observer.observe(
        this.$el,
        { subtree: true, characterData: true }
      );
    }
  },
}
</script>
