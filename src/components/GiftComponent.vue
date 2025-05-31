<script setup lang="ts">
import {BButton, BCard, BCardSubtitle, BCardText} from "bootstrap-vue-next";
import QRModal from "@/components/QRModal.vue";
import {ref} from "vue";
import type Gift from "@/gift.ts";

const props = defineProps<Gift>()
const modalShow = ref(false);
</script>

<template>
  <QRModal :gift="props" :show="modalShow" @close="modalShow=false"/>
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
    <BButton variant="primary" @click="modalShow=true">Comprar</BButton>
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