<template>
  <draggable
    class="dragArea"
    tag="ul"
    :group="{ name: 'g1' }"
    :list="pages"
  >
    <li v-for="page in pages" :key="page.id">
      <p>{{ page.name }}</p>
        <nested-draggable
            :pages="page.pages"
        ></nested-draggable>
    </li>
  </draggable>
</template>
<script>
import draggable from "vuedraggable";
export default {
  components: {
    draggable
  },
  props: {
    pages: {
      required: true,
      type: Array,
      default: []
    }
  },
  name: "nested-draggable"
};
</script>
<style scoped>
.dragArea {
  min-height: 50px;
  outline: 1px dashed;
}
</style>