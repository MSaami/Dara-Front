<template>
    <div>
        <div class="relative">
            <div class="flex flex-col justify-around absolute bottom-415 left-36">
                <div class="flex mr-8">
                    <div v-if="isSuccess" class="text-secondary-color font-bold text-2xl mr-36 text-center">
                        پرداخت با موفقیت انجام شد
                    </div>
                </div>
            </div>
        </div>
        <div v-for="data in installments" class="flex justify-center mb-48">
            <div class="style w-3/4">
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> مبلغ قسط </label>
                    <div class="stylelists w-36 bg-white"> {{ data.amount }} </div>
                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> وضعیت </label>
                    <div v-if="data.status == 'paid'" class="stylelists w-36 bg-green-400">
                        <b> پرداخت شده </b>
                    </div>
                    <div v-if="data.status == 'unpaid'" class="stylelists w-36 bg-red-400">
                        <b> پرداخت نشده </b>
                    </div>
                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> تاریخ سررسید </label>
                    <div class="stylelists w-36 bg-white"> {{ data.due_date }} </div>
                </div>
                <button @click="payment(data.id)"
                    class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold mt-auto mb-6">
                    پرداخت
                </button>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            installments: [],
            id: null,
            loan_id: null,
            amount: null,
            status: null,
            due_date: null,
            loanId: null,
            isSuccess: false,
        }
    },
    methods: {
        getData() {
            fetch('http://5.42.94.18:3000/api/v1/loan/' + this.$route.params.loanId + '/installment')
                .then(response => response.json())
                .then(response => {
                    this.installments = response.data
                });
        },
        payment(installmentId) {
            const requestOptions = {
                method: "PUT",
                headers: { "Content-Type": "application/json" },
            };
            fetch('http://5.42.94.18:3000/api/v1/installment/' + installmentId + '/pay', requestOptions)
                .then(response => {
                    if (response.status === 204) {
                        this.isSuccess = true;
                        this.afterPayment()
                    }
                })
        },
        afterPayment() {
            this.getData()

        },
    },
    created() {
        this.getData()
    }

}
</script>
<style scoped>
.style {
    @apply h-44 mr-52 justify-around items-start pt-9 border rounded-xl text-secondary-color flex flex-row -mt-96
}

.stylelists {
    @apply h-8 flex items-center border rounded-xl text-main-color pr-3
}
</style>