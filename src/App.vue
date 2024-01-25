<script setup>
import Balance from "./components/Balance.vue";
import Header from "./components/Header.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransactions from "./components/AddTransactions.vue";

// global state
import { ref, computed, onMounted } from "vue";
const transactions = ref([]);

// onMounted (useEffect of Vue)
onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem("transactions"));
  if (savedTransaction) {
    transactions.value = savedTransaction;
  }
});
// Get Total balance
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get Expense
const expense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount <= 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    ...transactionData,
  });
  saveTransactionToLocalStorage();
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 10000);
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  console.log(transactions.value);
  saveTransactionToLocalStorage();
};

const saveTransactionToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expense="+expense" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransactions @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>
