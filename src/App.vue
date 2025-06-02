<script setup lang="ts">
import GiftComponent from "@/components/GiftComponent.vue";
import type Gift from "@/gift.ts";
import {ref} from "vue";

const gifts = ref<{[key: string]: Gift;}>({});
fetch("/api/gifts").then((res) => {
  if (res.ok) {
    res.json().then((data: Gift[]) => {
      const giftsMap: {[key: string]: Gift;} = {};
      data.forEach(gift => {
        giftsMap[gift.name] = gift;
      });
      gifts.value = giftsMap;
    });
  } else {
    console.error("Failed to fetch gifts:", res.statusText);
  }
}).catch(err => {
  console.error("Error fetching gifts:", err);
})
</script>

<template>
  <header>
    <div>
      <h1>Ajude a pagar a festa!</h1>
      <p>Escolha um item desse site para fazer um PIX por QR Code, meu sistema automaticamente comprará o item. Caso não queira escolher algo em específico, esse QR Code é meu pix normal, e eu vou só considerar isso um presente. Caso queira ajudar com uma quantia especifica, não é necessário comprar um item inteiro, mas poderá especificar que seu dinheiro irá ajudar a comprar um item específico. Por exemplo, é possível comprar meio Jack Daniel's, ou ajudar a pagar uma askov com 2,50</p>
    </div>
    <img src="/assets/qrcode-main.png" alt="QR Code para Presente">
  </header>
  <main>
    <h1>Lista de Presentes</h1>
    <div id="shopping-list">
      <template v-for="gift in gifts">
        <GiftComponent :name="gift.name" :description="gift.description" :price="gift.price" :invested="gift.invested" @update-db="gift => {gifts[gift.name] = gift}"/>
      </template>
    </div>
  </main>
</template>

<style scoped>
header {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap-reverse;
  gap: 32px;
  min-height: 30vh;
  margin-top: 32px;
}

header > * {
  width: 25%;
  height: 100%;
}

header > img {
  max-height: 30vh;
  width: auto;
}

main {
  margin-top: 32px;
  width: 100%;
}

#shopping-list {
  width: 100%;
  display: flex;
  justify-content: space-evenly;
  align-items: flex-start;
  flex-wrap: wrap;
  gap: 16px;
}

#shopping-list > * {
  flex-basis: 24%;
}

@media (max-width: 700px)
{
  header > * {
    width: 100vw;
    height: 100%;
  }

  #shopping-list > * {
    flex-basis: 48%;
  }
}
</style>
