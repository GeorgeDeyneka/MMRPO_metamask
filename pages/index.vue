<script setup lang="ts">
const isMetamaskSupported = ref(false);
const isLoggedIn = ref(false);
const address = ref([]);
let computedAddress = computed(() => {
  return address.value;
});

onMounted(() => {
  isMetamaskSupported.value = (window as any).ethereum !== "undefined";
  address.value = JSON.parse(localStorage.getItem("account")!) || [];
});

watch(computedAddress, checkLoggedIn);

function checkLoggedIn() {
  isLoggedIn.value = address.value.length > 0;
  console.log(isLoggedIn.value);

  if (!isLoggedIn) {
    logoutWallet();
  }
}

function logoutWallet() {
  address.value = [];
  localStorage.removeItem("account");
}

async function connectWallet() {
  const accounts = await (window as any).ethereum.request({
    method: "eth_requestAccounts",
  });

  address.value = accounts[0];
  localStorage.setItem("account", JSON.stringify(address.value));
}
</script>

<template>
  <main class="container home">
    <div>
      <h1 class="home__title">Metamask Login</h1>

      <div v-if="isLoggedIn">Wallet id: {{ computedAddress }}</div>

      <div class="home__content" v-else>
        <h3 v-if="isMetamaskSupported" class="home__subtitle">
          Here you can connect your wallet
        </h3>
        <button
          @click="connectWallet"
          v-if="isMetamaskSupported"
          class="home__button"
        >
          Login Metamask
        </button>
      </div>
    </div>
  </main>
</template>

<style lang="scss">
.container {
  max-width: 1100px;
  margin: 0 auto;
}

.home {
  &__title {
    padding: 20px 0;
  }

  &__subtitle {
    padding: 10px 0;
  }

  &__button {
    padding: 8px 30px;
    border-radius: 5px;
    border: none;
    background-color: #5b3fcb;
    color: #fff;
    cursor: pointer;
  }

  &__content {
    margin: 35px 0;
  }
}
</style>
