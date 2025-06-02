<script setup lang="ts">
import {BButton, BCard, BCardSubtitle, BCardText} from "bootstrap-vue-next";
import QRModal from "@/components/QRModal.vue";
import {ref} from "vue";
import type Gift from "@/gift.ts";

const props = defineProps<Gift>()
const emit = defineEmits<{
  (e: 'updateDb', gift: Gift): void;
}>();
const modalShow = ref(false);
</script>

<template>
  <QRModal :gift="props" :show="modalShow" @close="modalShow=false" @update-db="value => emit('updateDb', value)"/>
  <BCard overlay :title="props.name">
    <div>
      <img class="mx-auto" :src="'assets/' + props.name + '.png'" alt="Gift image"/>
    </div>
    <BCardSubtitle>
      R${{ props.price }}(R${{ props.invested }} investido, ou {{ props.invested / props.price }} de {{ props.name }})
    </BCardSubtitle>
    <BCardText>
      {{ props.description }}
    </BCardText>
    <BButton variant="primary" @click="modalShow=true">Comprar</BButton>
  </BCard>
</template>

<style scoped>
img {
  max-width: 128px;
  height: 256px;
  margin-bottom: 12px;
}
div {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>