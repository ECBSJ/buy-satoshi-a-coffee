<script setup lang="ts">
import { ref, onBeforeMount } from "vue";
import { CircleHelp } from "lucide-vue-next";
import { showConnect, UserSession, openContractCall } from "@stacks/connect";
import { Cl, Pc, PostConditionMode } from "@stacks/transactions";
import logo from "./assets/logo.svg";
import Tributes from "./components/Tributes.vue";
import { createClient } from "@stacks/blockchain-api-client";
import { Buffer } from "buffer";
import type { TransactionEventSmartContractLog } from "./utils/types";

let userSession = new UserSession();

const tooltip = ref(
  "It's a friendly metaphor, not a real coffee. This acts as your personal tribute to Satoshi Nakamoto."
);

const name = ref("");
const message = ref("");
const amount = ref(1);
const isConnected = ref(false);

const selectAmount = (e: MouseEvent) => {
  const target = e.target as HTMLElement;
  amount.value = parseInt(target.id);
};

const handleSubmit = () => {
  console.log(name.value, message.value, amount.value);

  let pc = Pc.principal(userSession.loadUserData().profile.stxAddress.mainnet)
    .willSendEq(amount.value)
    .ft("SM3VDXK3WZZSA84XXFKAFAF15NNZX32CTSG82JFQ4.sbtc-token", "sbtc-token");

  openContractCall({
    contractAddress: "SM3VDXK3WZZSA84XXFKAFAF15NNZX32CTSG82JFQ4",
    contractName: "sbtc-token",
    functionName: "transfer",
    functionArgs: [
      Cl.uint(amount.value),
      Cl.standardPrincipal(userSession.loadUserData().profile.stxAddress.mainnet),
      Cl.standardPrincipal("SP1HEJ1XHBJZJFNA2AECYQXQGZD8EQE4F30D6H5CR"),
      Cl.some(Cl.bufferFromUtf8(`${name.value}☕️${message.value}`)),
    ],
    postConditions: [pc],
    postConditionMode: PostConditionMode.Deny,
    network: "mainnet",
    appDetails: {
      name: "Tribute to Satoshi",
      icon: window.location.origin + "/favicon.ico",
    },
    userSession: userSession,
    onFinish: (data) => {
      console.log(data);
    },
    onCancel: () => {
      console.log("Transaction cancelled");
    },
  });
};

const handleConnect = async () => {
  await showConnect({
    appDetails: {
      name: "Tribute to Satoshi",
      icon: logo,
    },
    onFinish: () => {
      isConnected.value = true;
      console.log(userSession.loadUserData().profile);
    },
  });
};

const handleDisconnect = () => {
  userSession.signUserOut();
  isConnected.value = false;
};

onBeforeMount(() => {
  if (userSession.isUserSignedIn()) {
    isConnected.value = true;
    console.log(userSession.loadUserData().profile);
  }

  getCoffeeEvents();

  // setInterval(() => {
  //   getCoffeeEvents();
  // }, 5000);
});

const client = createClient({
  baseUrl: "https://api.mainnet.hiro.so",
});

let results = ref<TransactionEventSmartContractLog[]>([]);

const getCoffeeEvents = async () => {
  const { data } = await client.GET("/extended/v1/contract/{contract_id}/events", {
    params: {
      path: { contract_id: "SM3VDXK3WZZSA84XXFKAFAF15NNZX32CTSG82JFQ4.sbtc-token" },
    },
    headers: {
      "x-api-key": import.meta.env.VITE_PUBLIC_HIRO_API_KEY!,
    },
  });

  let events = data?.results as TransactionEventSmartContractLog[];

  let coffees = events.filter((e) => {
    return e.contract_log.value.repr.includes("e29895efb88f");
  });

  let adjustedCoffees = coffees.map((c) => {
    let split = c.contract_log.value.repr.slice(2).split("e29895efb88f");

    let name = Buffer.from(split[0], "hex").toString("utf8");
    let message = Buffer.from(split[1], "hex").toString("utf8");
    let combined = `${name}☕️${message}`;

    return {
      ...c,
      contract_log: {
        ...c.contract_log,
        value: {
          hex: c.contract_log.value.hex,
          repr: combined,
        },
      },
    };
  });

  console.log(adjustedCoffees);

  results.value = adjustedCoffees;
};
</script>

<template>
  <button class="connect-button" v-on:click="handleDisconnect" v-if="isConnected">
    Disconnect
  </button>
  <button class="connect-button" type="button" v-on:click="handleConnect" v-else>Connect</button>
  <main>
    <section class="tribute-card">
      <p class="heading">
        Buy Satoshi coffee with <img src="/sbtc-round.png" width="35px" alt="sbtc-round-logo" />
        <span class="tooltip-logo" v-bind:title="tooltip"><CircleHelp /></span>
      </p>
      <div class="selection">
        <span class="coffee-icon">☕️</span>
        <span class="x">x</span>
        <span
          class="quantity-selector"
          :class="{ selected: amount === 1 }"
          id="1"
          v-on:click="selectAmount"
          >1</span
        >
        <span
          class="quantity-selector"
          :class="{ selected: amount === 2 }"
          id="2"
          v-on:click="selectAmount"
          >2</span
        >
        <span
          class="quantity-selector"
          :class="{ selected: amount === 3 }"
          id="3"
          v-on:click="selectAmount"
          >3</span
        >
        <span class="quantity-selector-total">{{ amount }}</span>
      </div>
      <input type="text" placeholder="Name / BNS / X handle" v-model="name" />
      <textarea type="text" placeholder="Write a tribute message..." v-model="message"></textarea>
      <small
        >This message, along with your name, will be inserted into the <code>memo</code> field of
        the transaction. Fees will vary depending on <code>memo</code> length.</small
      >
      <button v-on:click="handleSubmit" :disabled="!isConnected">
        Make Your {{ amount }} sBTC sat tribute
      </button>
      <small>Secured by Stacks</small>
    </section>

    <Tributes :coffees="results" />
  </main>
</template>

<style scoped>
.tooltip-logo {
  display: flex;
}

.connect-button {
  position: absolute;
  right: 0;
  padding: 10px 17px;
  border-radius: 7px;
  font-size: 1rem;
}

.selected {
  background-color: rgb(244, 200, 118);
  color: white;
}

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

button[disabled] {
  background-color: rgb(255, 231, 187);
  border: 1px solid rgb(255, 231, 187);
  color: rgb(133, 133, 133);
  cursor: not-allowed;
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
  margin-top: 200px;
}

section {
  width: 500px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  row-gap: 20px;
  border: 3px solid orange;
  padding: 30px;
  border-radius: 15px;
}
</style>
