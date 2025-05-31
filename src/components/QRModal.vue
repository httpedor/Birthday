<script setup lang="ts">
import {BFormGroup, BFormInput, BImg, BInputGroup, BModal} from "bootstrap-vue-next";
import {ref, watch} from "vue";
import type Gift from "@/gift.ts";
import BrCode from '@/lib/br_code.js';
import QRCode from 'qrcode'
import {watchImmediate} from "@vueuse/core";

const key = "75d4c7b7-21e7-47f4-b575-0a05da455e9c"
const keyType = "Outro";
const name = "Pedro Henrique";
const city = "Toledo";

const props = defineProps<{
  gift: Gift;
  show: boolean;
}>();
const emits = defineEmits<{
  close: []
}>();
const selectedPrice = ref(props.gift.price.toString());
const qrCode = ref("");
const percentage = ref(0);

watchImmediate(selectedPrice, (newVal, oldVal) => {
  let brCode = new BrCode(key, newVal, name, "a fazer", keyType, city);
  let code = brCode.generate_qrcp();
  percentage.value = (parseFloat(newVal) / props.gift.price) * 100;
  QRCode.toDataURL(code, {width: 256})
    .then(url => {
      qrCode.value = url;
    })
    .catch(err => {
      console.error("Error generating QR code:", err);
    });
})

</script>

<template>
  <BModal v-model="props.show" :title="props.gift.name" centered @hide="$emit('close')">
    <div>
      <BImg :src="qrCode"/>
    </div>
    <BInputGroup prepend="R$" :append="percentage + '%'">
      <BFormInput v-model="selectedPrice" type="number" min="0"/>
    </BInputGroup>
  </BModal>
</template>

<style scoped>
div {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 16px;
}
</style>