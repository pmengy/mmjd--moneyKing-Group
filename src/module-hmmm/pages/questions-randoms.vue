<template>
  <div class="app-container">
    精选题库

    <alert-tip :total="10" />
  </div>
</template>

<script>
import AlertTip from "../components/alert-tip.vue";
export default {
  name: "QuestionsRandoms",
  components: { AlertTip },
};
</script>

<style scoped lang="less"></style>
