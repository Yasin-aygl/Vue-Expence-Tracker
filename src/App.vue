<template>
    <div>
        <AppHeader />
        <div class="container">
            <Balance :total="total" />
            <IncomeExpenses :income="+income" :expenses="+expenses" />
            <TransactionsList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
            <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
        </div>
    </div>
</template>

<script setup>
import AppHeader from "./components/AppHeader.vue"
import Balance from "./components/Balance.vue"
import IncomeExpenses from "./components/IncomeExpenses.vue"
import TransactionsList from "./components/TransactionsList.vue"
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";

import { ref, computed, onMounted } from "vue"

const toast = useToast()



const transactions = ref([]);

onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

    if (savedTransactions) {
        transactions.value = savedTransactions;
    }

})

// total 
const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
        return acc + transaction.amount;
    }, 0)
})

// get income
const income = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount
        }, 0)
        .toFixed(2)
})

// get expenses
const expenses = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount < 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount
        }, 0)
        .toFixed(2)
})

// add Transaction
const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount,
    })

    saveTransactionsToLocalStorage()

    toast.success("Transactions added")
}

// GENERATE UNİQUE ID 
const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000)
}

// DELETE TRANSACTİON 
const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

    saveTransactionsToLocalStorage();

    toast.success("Transactions deleted");
}

const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>