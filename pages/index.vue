<script setup lang="ts">
const isMetamaskSupported = ref(false);
const isLoggedIn = ref(false);
const address = ref([]);
let computedAddress = computed(() => {
  return address.value;
});

onMounted(() => {
  isMetamaskSupported.value = (window as any).ethereum !== "undefined";
  checkConnection();
});

function checkConnection() {
  (window as any).ethereum
    .request({ method: "eth_accounts" })
    .then(handleAccountsChanged)
    .catch(console.error);
}

function handleAccountsChanged(accounts: any) {
  if (accounts.length === 0) {
    logoutWallet();
  } else {
    address.value = accounts[0];
    isLoggedIn.value = address.value.length > 0;
  }
}

function logoutWallet() {
  address.value = [];
  isLoggedIn.value = false;
}

async function loginWallet() {
  (window as any).ethereum
    .request({
      method: "eth_requestAccounts",
    })
    .then(handleAccountsChanged)
    .catch(console.error);
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
          @click="loginWallet"
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
