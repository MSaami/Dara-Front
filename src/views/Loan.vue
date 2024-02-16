<template>
    <div>
        <div class="relative">
            <div class="flex flex-col justify-around absolute  bottom-415 left-36">
                <RouterLink to="/new-loan">
                    <div class="flex mr-8">
                        <button class="h-9 border w-32 rounded-xl bg-secondary-color text-main-color font-bold">
                            تسهیلات جدید
                        </button>
                    </div>
                </RouterLink>
            </div>
        </div>
        <div v-for="data in datas" class="flex justify-center mb-48">
            <div class="style w-3/4">
                <RouterLink :to="{ name: 'LoanDetails', params: { loanId: data.id } }">
                    <div class="inline-flex items-start flex-col">
                        <label class="mb-3"> تسهیلات </label>
                        <div class="stylelists w-36">{{ data.title }}</div>
                    </div>
                </RouterLink>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> تعداد قسط </label>
                    <div class="stylelists w-36">{{ data.number_of_installment }}</div>
                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> مبلغ کل </label>
                    <div class="stylelists w-36">{{ data.amount }}</div>
                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> اقساط پرداخت شده </label>
                    <div class="stylelists w-36">{{ data.number_of_paid }}</div>
                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> توضیحات </label>
                    <div class="stylelists w-44">{{ description }}</div>
                </div>
                <div class="inline-flex mt-auto mb-4">
                    <button class="ml-4 h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold "> حذف
                    </button>
                    <RouterLink :to="{ name: 'EditLoan', params: { loanId: data.id } }">
                        <button class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold "> ویرایش
                        </button>
                    </RouterLink>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            datas: [],
            title: null,
            number_of_installment: null,
            amount: null,
            number_of_paid: null,
            loanId: null
        }
    },
    methods: {
        getLoan() {
            fetch('http://5.42.94.18:3000/api/v1/wallet/2/loan')
                .then(response => response.text())
                .then(response => JSON.parse(response))
                .then(response => {
                    this.datas = response.data
                })
        }
    },
    created() {
        this.getLoan()
    },
}
</script>
<style scoped>
.style {
    @apply h-44 mr-52 justify-around items-start pt-9 border rounded-xl text-secondary-color flex flex-row -mt-96
}

.stylelists {
    @apply h-8 flex items-center border rounded-xl bg-white text-main-color pr-3
}
</style>