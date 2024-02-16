<template>
  <div class="flex justify-center">
    <div class="relative">
        <div class="flex flex-col justify-around absolute  bottom-415 " style="width: 500px; font-size: 15px;">
          <div>مبلغ خرج شده  تا به الان:‌ {{ total_expenses }}</div>
          <div>بودجه این ماه: {{ budget_in_human }}</div>
          <div>مبلغ خرج‌شده از بودجه : {{ spend_budget_based_budget_in_human }}</div>
  </div>
    </div>
  </div>
</template>
<script >
export default {
  data(){
    return {
      total_expenses: null,
      spend_budget_based_budget_in_human: null,
      budget_in_human: null
    }
  },
  methods: {
    getReport(){
      fetch('http://5.42.94.18:3000/api/v1/wallet/1/report/budget')
      .then(response => response.text())
      .then(response => JSON.parse(response))
      .then(response => {
        this.total_expenses = response.data.spend_budget_based_transaction_in_human
        this.spend_budget_based_budget_in_human = response.data.spend_budget_based_budget_in_human
        this.budget_in_human = response.data.budget_in_human
      })
    },
  },
  created(){
    this.getReport()
  }
}

</script>
