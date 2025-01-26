<script setup lang="ts">
import { ref } from "vue";
import { CircleHelp } from "lucide-vue-next";

const tooltip = ref(
  "It's a friendly metaphor, not a real coffee. This acts as your personal tribute to Satoshi Nakamoto."
);
</script>

<template>
  <main>
    <section class="tribute-card">
      <p class="heading">
        Buy Satoshi a coffee <span v-bind:title="tooltip"><CircleHelp /></span>
      </p>
      <div class="selection">
        <span class="coffee-icon">☕️</span>
        <span class="x">x</span>
        <span class="quantity-selector">1</span>
        <span class="quantity-selector">2</span>
        <span class="quantity-selector">3</span>
        <span class="quantity-selector-total">qt</span>
      </div>
      <input type="text" placeholder="Name / BNS / X handle" />
      <textarea type="text" placeholder="Write a tribute message..."></textarea>
      <small
        >This message, along with your name, will be inserted into the <code>memo</code> field of
        the transaction. Fees will vary depending on <code>memo</code> length.</small
      >
      <button>Make Your $25 Tribute</button>
      <small>Secured by Stacks</small>
    </section>
  </main>
</template>

<style scoped>
.x {
  font-size: 1.5rem;
  font-weight: bold;
  color: lightslategray;
}

button {
  padding: 15px 10px;
  background-color: orange;
  border: 1px solid orange;
  border-radius: 28px;
  font-size: 1.3rem;
  font-weight: bold;
  cursor: pointer;
}

.coffee-icon {
  font-size: 3rem;
}

.quantity-selector {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.2rem;
  font-weight: bolder;
  color: sienna;
  width: 46px;
  height: 46px;
  background-color: white;
  border: 1px solid rgb(255, 231, 187);
  border-radius: 25px;
  cursor: pointer;
}

.quantity-selector:hover {
  background-color: rgb(244, 200, 118);
  border-color: rgb(244, 200, 118);
  color: white;
}

.quantity-selector-total {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.2rem;
  font-weight: bolder;
  color: rgb(133, 133, 133);
  width: 46px;
  height: 46px;
  background-color: white;
  border: 1px solid rgba(194, 194, 194, 0.407);
  border-radius: 8px;
}

input {
  width: 100%;
  padding: 15px 10px;
  border-radius: 5px;
  border: 1px solid rgb(229, 229, 229);
  background-color: rgb(229, 229, 229);
  font-weight: bold;
  font-size: 1.2rem;
}

input:active,
input:focus {
  border: 1px solid orange;
  background-color: white;
}

textarea {
  width: 100%;
  padding: 15px 10px;
  border-radius: 5px;
  border: 1px solid rgb(229, 229, 229);
  background-color: rgb(229, 229, 229);
  font-weight: bold;
  font-size: 1.2rem;
}

textarea:active,
textarea:focus {
  border: 1px solid orange;
  background-color: white;
}

.selection {
  display: flex;
  justify-content: space-between;
  align-items: center;
  column-gap: 10px;
  background-color: rgba(245, 233, 228, 0.155);
  border: 1px solid rgba(255, 166, 0, 0.399);
  border-radius: 15px;
  padding: 1px 35px;
}

.heading {
  font-size: 1.7rem;
  font-weight: bold;
  text-align: left;
  display: flex;
  align-items: center;
  column-gap: 10px;
}

main {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  row-gap: 30px;
}

section {
  width: 500px;
  display: flex;
  flex-direction: column;
  align-items: left;
  justify-content: center;
  row-gap: 20px;
  border: 3px solid orange;
  padding: 30px;
  border-radius: 15px;
}
</style>
