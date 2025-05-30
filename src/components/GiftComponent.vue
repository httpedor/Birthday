<script setup lang="ts">
import {BButton, BCard, BCardImg, BCardSubtitle, BCardText} from "bootstrap-vue-next";
import type Gift from "../gift.ts"

const props = defineProps<Gift>()
const emit = defineEmits<{
  clicked: [Gift]
}>()
</script>

<template>
<BCard overlay :title="props.name">
  <div>
    <img class="mx-auto" :src="'src/assets/' + props.name + '.png'" alt="Gift image"/>
  </div>
  <BCardSubtitle>
    R${{ props.price }}
  </BCardSubtitle>
  <BCardText>
    {{ props.description }}
  </BCardText>
  <BButton variant="primary" @click="$emit('clicked', props)">Comprar</BButton>
</BCard>
</template>

<style scoped>
img {
  max-width: 128px;
  height: 256px;
}
div {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>