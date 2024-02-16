<template>
    <div>
        <div  class="flex justify-center mb-48">
            <div class="style w-3/4">
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> تسهیلات </label>
                    <input type="text" v-model="title" class="stylelists w-36">

                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> مبلغ کل </label>
                    <input type="number" v-model="amount" class="stylelists w-36">
                </div>
                <div class="inline-flex justify-end">
                    <button @click="editLoan()"
                        class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold mt-14 mr-8">
                        ثبت
                    </button>
                </div>
            </div>
        </div>
        <div v-if="isSuccess" class="text-green-500 font-bold text-2xl mt-11 text-center">
            ویرایش با موفقیت انجام شد
        </div>
        <div v-if="isErrors" v-for="error in errors" class="text-secondary-color font-bold text-f20 mt-11 text-end ml-435">
            {{ error }}
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            datas: [],
            title: null,
            amount: null,
            isSuccess: false,
            isError: false,
            loanId: null,
            errors:[],
        }
    },
    methods: {
        getLoan() {
            fetch('http://5.42.94.18:3000/api/v1/loan/' + this.$route.params.loanId)
                .then(response => response.text())
                .then(response => JSON.parse(response))
                .then(response => {
                    this.title = response.data.title
                    this.amount = response.data.amount
                })
        },
        editLoan() {
            const requestOptions = {
                method: "PUT",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ title:this.title, amount:this.amount })
            };
            fetch('http://5.42.94.18:3000/api/v1/loan/' + this.$route.params.loanId, requestOptions)
                .then(response => {
                    if (response.status == 204) {
                        this.isSuccess = true
                    }
                    if (response.status == 442) {
                        response.json()
                            .then(khata => {
                                this.isError = true
                                this.errors = khata.errors
                            })
                    }
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
    @apply h-8 flex items-center border rounded-xl pr-3 bg-white text-main-color
}
</style>