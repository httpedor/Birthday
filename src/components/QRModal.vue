<script setup lang="ts">
import {
  BButton,
  BFormInput,
  BImg,
  BInputGroup, BLink,
  BModal, BSpinner,
  BTooltip
} from "bootstrap-vue-next";
import {computed, ref, useTemplateRef, watch} from "vue";
import type Gift from "@/gift.ts";
import BrCode from '@/lib/br_code.js';
import QRCode from 'qrcode'
import {watchImmediate} from "@vueuse/core";

const key = "75d4c7b7-21e7-47f4-b575-0a05da455e9c"
const keyType = "Outro";
const name = "Pedro Henrique";
const city = "Toledo";

function generateBrCode(price: number): string {
  let brCode = new BrCode(key, price.toString(), name, "a fazer", keyType, city);
  return brCode.generate_qrcp();
}

async function sendReceipt() {
  const fInput = file.value;
  if (fInput == null || !fInput.files || fInput.files.length === 0) {
    alert("Por favor, selecione um arquivo para enviar.");
    return;
  }
  const fileB64 = await new Promise<string>((resolve, reject) => {
    if (fInput == null || !fInput.files || fInput.files.length === 0) {
      alert("Por favor, selecione um arquivo para enviar.");
      return;
    }
    const reader = new FileReader();
    reader.onload = () => resolve(reader.result as string);
    reader.onerror = reject;
    reader.readAsDataURL(fInput.files[0]);
  });
  receiptState.value = 1;
  const response = await fetch("/api/validate", {
    method: "POST",
    headers:{
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      file: fileB64, //TODO: Serialize to B64
      item: props.gift.name,
      price: selectedPrice.value,
    })
  });
  if (response.ok) {
    receiptState.value = 2;
    response.json().then(data => {
      console.log(data)
      emit('updateDb', data.updated as Gift);
    })
  } else {
    receiptState.value = 3;
    const errorText = await response.text();
    alert("Erro ao enviar comprovante: " + errorText);
  }
}

const props = defineProps<{
  gift: Gift;
  show: boolean;
}>();
const emit = defineEmits<{
  (e: 'close'): void;
  (e: 'updateDb', value: Gift): void;
}>();
const file = useTemplateRef("file");
const selectedPrice = ref(props.gift.price);
const receiptState = ref(0); // 0 = not sent, 1 = sent, 2 = confirmed, 3 = failed
const copied = ref(false);
const qrCode = ref("");
const pixCode = ref("");
const percentage = computed({
  get: () => (selectedPrice.value / props.gift.price) * 100,
  set: (newVal) => {
    selectedPrice.value = ((newVal / 100) * props.gift.price);
  }
})

watchImmediate(selectedPrice, (newVal, oldVal) => {
  let code = generateBrCode(newVal);
  pixCode.value = code;
  QRCode.toDataURL(code, {width: 256})
      .then(url => {
        qrCode.value = url;
      })
      .catch(err => {
        console.error("Error generating QR code:", err);
      });
})
watch(copied, (newVal) => {
  if (newVal) {
    navigator.clipboard.writeText(pixCode.value)
        .then(() => {
          setTimeout(() => copied.value = false, 2000);
        })
        .catch(err => {
          console.error("Failed to copy text:", err);
        });
  }
})
</script>

<template>
  <BModal v-model="props.show" :title="props.gift.name" centered @hide="emit('close')">
    <div class="center">
      <BImg :src="qrCode"/>
    </div>
    <BInputGroup prepend="Copia e Cola">
      <BFormInput readonly type="text" v-model="pixCode"/>
      <BTooltip>
        <template #target>
          <BButton :variant="copied ? 'success' : 'secondary'" title="Copiar" @click="copied = true">
            <BIcon :icon="copied ? 'check2' : 'copy'"/>
          </BButton>
        </template>
        {{copied ? 'Copiado!' : 'Copiar'}}
      </BTooltip>
    </BInputGroup>
    <div class="center">
      <BInputGroup prepend="R$">
        <BFormInput v-model="selectedPrice" type="number" min="0"/>
      </BInputGroup>
      <BInputGroup prepend="%">
        <BFormInput v-model="percentage" type="number" min="0"/>
      </BInputGroup>
    </div>
    <template v-if="receiptState === 0">
      <div class="center">
        <input type="file" ref="file" style="display: none" @change="sendReceipt"/>
        <BButton variant="primary" @click="file?.click()">Enviar Comprovante</BButton>
      </div>
    </template>
    <template v-else-if="receiptState === 1">
      <div class="center flex-column">
        <p>Comprovante enviado. Validando... </p>
        <BSpinner />
      </div>
    </template>
    <template v-else-if="receiptState === 2" e>
      <div class="center flex-column">
        <p>Comprovante validado com sucesso.</p>
      </div>
    </template>
  </BModal>
</template>

<style scoped>
.center{
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin-bottom: 16px;
}
</style>