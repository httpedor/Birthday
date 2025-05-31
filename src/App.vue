<script setup lang="ts">
import GiftComponent from "@/components/GiftComponent.vue";
import type Gift from "@/gift.ts";
const gifts: Gift[] = [
  {
    name: "Askov Pura",
    description: "Uma garrafa de 900ml de vodka Askov Tridestilada",
    price: 25
  },
  {
    name: "Askov Azul",
    description: "Uma garrafa de 900ml de vodka Askov Blueberry(Mirtilo)",
    price: 25
  },
  {
    name: "Askov Vermelha",
    description: "Uma garrafa de 900ml de vodka Askov Morango",
    price: 25
  },
  {
    name: "Askov Limão",
    description: "Uma garrafa de 900ml de vodka Askov Limão",
    price: 25
  },
  {
    name: "Jack Daniel's",
    description: "Uma garrafa de 1L de whiskey Jack Daniel's",
    price: 200
  },
  {
    name: "Jack Daniel's Honey",
    description: "Uma garrafa de 1L de whiskey Jack Daniel's sabor mel",
    price: 200
  },
  {
    name: "Jack Daniel's Fire",
    description: "Uma garrafa de 1L de whiskey Jack Daniel's Fire",
    price: 200
  },
  {
    name: "Jack Daniel's Green Apple",
    description: "Uma garrafa de 1L de whiskey Jack Daniel's sabor maçã verde",
    price: 200
  },
  {
    name: "Gramixinga",
    description: "A localização da festa. Ajude a pagar o aluguel do espaço.",
    price: 650
  },
  {
    name: "Caixa Heineken",
    description: "Uma caixa com 6 longnecks de 330ml de cerveja Heineken",
    price: 70
  }
]
</script>

<template>
  <header>
    <div>
      <h1>Ajude a pagar a festa!</h1>
      <p>Escolha um item desse site para fazer um PIX por QR Code, meu sistema automaticamente comprará o item. Caso não queira escolher algo em específico, esse QR Code é meu pix normal, e eu vou só considerar isso um presente. Caso queira ajudar com uma quantia especifica, não é necessário comprar um item inteiro, mas poderá especificar que seu dinheiro irá ajudar a comprar um item específico. Por exemplo, é possível comprar meio Jack Daniel's, ou ajudar a pagar uma askov com 2,50</p>
    </div>
    <img src="./assets/qrcode-main.png" alt="QR Code para Presente">
  </header>
  <main>
    <h1>Lista de Presentes</h1>
    <div id="shopping-list">
      <template v-for="gift in gifts">
        <GiftComponent :name="gift.name" :description="gift.description" :price="gift.price"/>
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
