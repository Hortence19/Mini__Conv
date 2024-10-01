<script setup lang="ts">
import { onMounted, ref } from 'vue';
import axios from 'axios';

const amount = ref(1);
const fromCurrency = ref('USD');
const toCurrency = ref('XOF');
const result = ref(null);
let id = 1;
const tab = ref<{ id: number, currency: string, rate: number }[]>([]);

const Convert = async () => {
    const apiKey = '62eeff11103a100cdff236e6';
    const response = await axios.get(`https://v6.exchangerate-api.com/v6/${apiKey}/latest/${fromCurrency.value}`);
    const rates = response.data;

    tab.value = [];

    for (const [currency, value] of Object.entries(rates.conversion_rates)) {
        tab.value.push({ id: id++, currency: currency, rate: value as number });
    }

    const toCurrencyRate = tab.value.find(item => item.currency === toCurrency.value);
    if (toCurrencyRate) {
        result.value = (amount.value * toCurrencyRate.rate).toFixed(2);
    } else {
        console.error('error:', toCurrency.value);
        result.value = null;
    }
};

onMounted(() => {
    Convert(); 
});
</script>

<template>
<header>
    <div class="container">
        <div class="header__content">
            <h1>CONVERTIO</h1>
        </div>
    </div>
</header>
<main>
    <section class="container">
        <div class="content">
            <form @submit.prevent="Convert">
                <div class="amount">
                    <p>Amount</p>
                    <input v-model="amount" placeholder="Enter amount" >
                </div>
                <div id="dropList">
                    <div class="from">
                        <p>From</p>
                        <div class="select-box">
                            <select v-model="fromCurrency">
                                <option v-for="item in tab" :key="item.id" :value="item.currency">{{ item.currency }}</option>
                            </select>
                        </div>
                    </div>
                    <img src="/src/assets/icones/icon.png" alt="">
                    <div class="to">
                        <p>To</p>
                        <div class="select-box">
                            <select v-model="toCurrency">
                                <option v-for="item in tab" :key="item.id" :value="item.currency">{{ item.currency }}</option>
                            </select>
                        </div>
                    </div>
                </div>
                <div class="result">
                    <button type="submit">Convert</button>
                    <p>{{ result }}</p>
                </div>
            </form>
        </div>
    </section>
</main>
</template>

<style scoped></style>
