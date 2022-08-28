<template>
  <client-only>
    <Flipbook
      class="flipbook"
      :pages="pages"
      v-slot="flipbook"
      ref="flipbook"
    ></Flipbook>
  </client-only>
</template>

<script>
export default {
  name: "IndexPage",
  data() {
    return {
      pages: [
        "https://images.pexels.com/photos/4380970/pexels-photo-4380970.jpeg?auto=compress&cs=tinysrgb&dpr=3&h=750&w=1260",
        "https://images.pexels.com/photos/2709385/pexels-photo-2709385.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
        "https://images.pexels.com/photos/2709385/pexels-photo-2709385.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
        "https://images.pexels.com/photos/3081752/pexels-photo-3081752.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
        "https://images.pexels.com/photos/3083250/pexels-photo-3083250.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
      ],
    };
  },
};
</script>
<style scoped>
.flipbook {
  width: 100vw;
  height: 100vh;
}
</style>
