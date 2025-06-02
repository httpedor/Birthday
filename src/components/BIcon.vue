<script setup lang="ts">
const props = defineProps<{
  icon: string;
}>();
</script>

<template>
<i class="bi" :class="'bi-' + props.icon"/>
</template>

<style scoped>

</style>