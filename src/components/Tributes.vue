<script setup lang="ts">
import { ref } from "vue";
import { CircleHelp } from "lucide-vue-next";
import type { TransactionEventSmartContractLog } from "@/utils/types";

const tooltip = ref(
  "It's a friendly metaphor, not a real coffee. This acts as your personal tribute to Satoshi Nakamoto."
);

const props = defineProps({
  coffees: Array<TransactionEventSmartContractLog>,
});
</script>

<template>
  <section>
    <p class="heading">
      Recent coffees <span class="tooltip-logo" v-bind:title="tooltip"><CircleHelp /></span>
    </p>
    <div class="tributes-container" v-for="(coffee, index) in coffees">
      <div class="tributes-container-top">
        <span>TXID: {{ coffee.tx_id.slice(0, 7) + "..." + coffee.tx_id.slice(-7) }}</span>
        <span>Index: {{ coffees!.length - 1 - index }}</span>
      </div>

      <div class="tributes-container-bottom">
        <span>{{ coffee.contract_log.value.repr.split("☕️")[0] }}</span>
        <span>{{ coffee.contract_log.value.repr.split("☕️")[1] }}</span>
      </div>
    </div>
  </section>
</template>

<style scoped>
.tooltip-logo {
  display: flex;
}

.tributes-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: rgba(241, 241, 241, 0.406);
  padding: 10px;
  border-radius: 10px;
}

.tributes-container-top {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.tributes-container-bottom {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 1.3rem;
  font-weight: bold;
}

.heading {
  font-size: 1.7rem;
  font-weight: bold;
  text-align: left;
  display: flex;
  align-items: center;
  column-gap: 10px;
}
</style>
