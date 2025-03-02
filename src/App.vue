<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransationList from './components/TransationList.vue';
import AddTransation from './components/AddTransation.vue';
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast();
const transactions = ref([

]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
})

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
})
//Get income
const income = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
})
//Get expenses
const expenses = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount < 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
})

const handleTransactionSubmitted = (transactionData) => {

  const transaction = {
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  }
  transactions.value = [...transactions.value, transaction];
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
  toast.success('Transaction added successfully');

}

const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000000);
}

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
  toast.success('Transaction deleted successfully');
}
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpense :income="+income" :expenses="+expenses" />
    <TransationList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransation @transactionSubmitted="handleTransactionSubmitted" />


  </div>
</template>