<template>
  <section class="flex items-center justify-between mb-10">
    <h1 class="text-4xl font-extrabold">Summary</h1>
    <div>
      <USelectMenu :options="transactionViewOptions" v-model="selectedView" />
    </div>
  </section>
  <section
    class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 sm:gap-16 mb-10"
  >
    <Trend
      color="green"
      title="Income"
      :amount="incomeTotal"
      :last-amount="prevIncomeTotal"
      :loading="pending"
    />
    <Trend
      color="red"
      title="Expense"
      :amount="expenseTotal"
      :last-amount="prevExpenseTotal"
      :loading="pending"
    />
    <Trend
      color="green"
      title="Investments"
      :amount="4000"
      :last-amount="3000"
      :loading="pending"
    />
    <Trend
      color="red"
      title="Saving"
      :amount="4000"
      :last-amount="4100"
      :loading="pending"
    />
  </section>
  <section class="flex justify-between mb-10">
    <div>
      <h2 class="text-2xl font-extrabold">Transactions</h2>
      <div class="text-gray-500 dark:text-gray-400">
        You have {{ incomeCount }} incomes and {{ expenseCount }} expenses this
        period
      </div>
    </div>
    <div>
      <TransactionModal v-model="isOpen" @saved="refresh()" />
      <UButton
        icon="i-heroicons-plus-circle"
        color="white"
        variant="solid"
        label="Add"
        @click="isOpen = true"
      />
    </div>
  </section>
  <section v-if="!pending">
    <div v-for="(transactionsOnDay, date) in byDate" :key="date" class="mb-10">
      <DailyTransactionSummary :date="date" :transactions="transactionsOnDay" />
      <Transaction
        v-for="transaction in transactionsOnDay"
        :key="transaction.id"
        :transaction="transaction"
        @deleted="refresh()"
        @edited="refresh()"
      />
    </div>
  </section>

  <section v-else>
    <USkeleton class="h-8 w-full mb-2" v-for="i in 4" :key="i" />
  </section>
</template>

<script setup>
import { transactionViewOptions } from "~/constants";
// const selectedView = ref(transactionViewOptions[1]);
const user = useSupabaseUser();
const selectedView = ref(
  user.value.user_metadata?.transaction_view ?? transactionViewOptions[1]
);

// const supabase = useSupabaseClient(); // moved to global store

// const transactions = ref([]); // moved to global store
// const isLoading = ref(false); // moved to global store
const isOpen = ref(false); // for modal

const { current, previous } = useSelectedTimePeriod(selectedView);

const {
  pending,
  refresh,
  transactions: {
    incomeCount,
    expenseCount,
    incomeTotal,
    expenseTotal,
    grouped: { byDate },
  },
} = useFetchTransactions(current);

const {
  refresh: refreshPrevious,
  transactions: {
    incomeTotal: prevIncomeTotal,
    expenseTotal: prevExpenseTotal,
  },
} = useFetchTransactions(previous);

await Promise.all([refresh(), refreshPrevious()]); //88 lesson

// await refresh(); // called in useFetchTransactions

// moved to global store
// const income = computed(() =>
//   transactions.value.filter((t) => t.type === "Income")
// );
// const expense = computed(() =>
//   transactions.value.filter((t) => t.type === "Expense")
// );
// const incomeCount = computed(() => income.value.length);
// const expenseCount = computed(() => expense.value.length);
// const incomeTotal = computed(() =>
//   income.value.reduce((sum, transaction) => sum + transaction.amount, 0)
// );
// const expenseTotal = computed(() =>
//   expense.value.reduce((sum, transaction) => sum + transaction.amount, 0)
// );

// moved to global store
// const fetchTransactions = async () => {
//   isLoading.value = true;

//   try {
//     const { data } = await useAsyncData("transactions", async () => {
//       const { data, error } = await supabase
//         .from("transactions")
//         .select()
//         .order("created_at", { ascending: false });
//       // order for sorting
//       if (error) {
//         return [];
//       }

//       return data;
//     });

//     return data.value;
//   } finally {
//     isLoading.value = false;
//   }
// };

// moved to global store
// const refreshTransactions = async () => {
//   transactions.value = await fetchTransactions();
// };
// await refreshTransactions();

// moved to global store
// const transactionsGroupedByDate = computed(() => {
//   let grouped = {};
//   for (const transaction of transactions.value) {
//     const date = new Date(transaction.created_at).toISOString().split("T")[0];
//     if (!grouped[date]) {
//       grouped[date] = [];
//     }
//     grouped[date].push(transaction);
//   }
//   // const sortedKeys = Object.keys(grouped).sort().reverse()
//   // const sortedGrouped = {}
//   // for (const key of sortedKeys) {
//   //   sortedGrouped[key] = grouped[key]
//   // }
//   // return sortedGrouped
//   return grouped;
// });
// console.log(transactionsGroupedByDate.value);
</script>
