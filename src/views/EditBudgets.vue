<template>
    <div>
        <div class="style w-2/3">
            <div class="inline-flex items-center flex-col">
                <label class="ml-28">دسته‌بندی</label>
                <select v-model="dastebandi" name="category" class="mr-4 my-3 px-3 w-56 h-8 rounded-xl text-black">
                    <option v-for="category in categoris" :value="category.id" class="text-black">
                        {{ category.name }}</option>
                </select>
                <p v-if="isWrongCategory">
                    <b class="text-red-600 text-lg mr-5">لطفا دسته‌بندی را وارد کنید</b>
                </p>
            </div>
            <div class="inline-flex items-center flex-col">
                <label class="ml-12">ماه</label>
                <div class="stylelists w-36"> {{ month }} </div>
            </div>
            <div class="inline-flex items-center flex-col">
                <label class="ml-12">سال</label>
                <div class="stylelists w-36"> {{ year }} </div>
            </div>
            <div class="inline-flex items-center flex-col">
                <label class="ml-12">مبلغ</label>
                <input dir="ltr" type="number" v-model="amount" @change="isWrongAmount = false" class="stylelists w-36"
                    :class="[isWrongAmount == true ? 'border-red-600 border-2' : 'border-gray-600']">
                <div class="mr-32" dir="ltr"> {{ amount.toLocaleString() }} </div>
                <p v-if="isWrongAmount">
                    <b class="text-red-600 text-lg mr-5">لطفا مبلغ را وارد کنید</b>
                </p>
            </div>
            <div class="inline-flex justify-end">
                <button @click="editBudget()"
                    class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold mt-14 mr-8">
                    ویرایش
                </button>
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
            categoris: [],
            dastebandi: null,
            amount: null,
            month: null,
            year: null,
            isSuccess: false,
            transactionId: null,
            isWrongAmount: false,
            isWrongCategory: false,
            isErrors: false,
            errors: [],
        }
    },
    methods: {
        getData() {
            fetch('http://5.42.94.18:3000/api/v1/budget/' + this.$route.params.budgetId)
                .then(response => response.text())
                .then(response => JSON.parse(response))
                .then(response => {
                    this.amount = response.data.amount
                    this.year = response.data.year
                    this.month = response.data.month
                    this.dastebandi = response.data.category_id
                });
        },
        getCategory() {
            fetch('http://5.42.94.18:3000/api/v1/category')
                .then(response => response.text())
                .then(response => JSON.parse(response))
                .then(response => {
                    this.categoris = response.data

                })
        },
        editBudget() {
            if (this.amount == null || this.amount <= 0) {
                this.isWrongAmount = true
            }
            if (this.dastebandi == null) {
                this.isWrongCategory = true
            }
            if (this.isWrongAmount == true || this.isWrongCategory == true) {
                return
            }
            const requestOptions = {
                method: "PUT",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ category_id: this.dastebandi, amount: this.amount, year: 1402, month: 4 })
            };
            fetch('http://5.42.94.18:3000/api/v1/budget/' + this.$route.params.budgetId, requestOptions)
                .then(response => {
                    if (response.status === 204) {
                        this.isSuccess = true;
                        this.$emit('updateWallet')
                    }
                    if (response.status === 422) {
                        response.json()
                            .then(khata => {
                                this.isErrors = true
                                this.errors = khata.errors
                            })
                    }

                })
        }
    },
    created() {
        this.getCategory()
        this.getData()

    }
}
</script>
<style scoped>
.style {
    @apply mr-80 h-32 border rounded-xl -mt-415 px-1 pt-4 text-secondary-color flex flex-row
}

.stylelists {
    @apply flex items-center mr-14 border my-3 py-3 px-4 rounded-xl h-8 bg-white text-main-color
}
</style>