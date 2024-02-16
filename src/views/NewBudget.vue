<template>
    <div class="flex justify-center">
        <div class="style w-2/3">
            <div class="inline-flex items-center flex-col">
                <label class="-mr-28">دسته‌بندی</label>
                <select @change="isWrongCategory = false; isSuccess = false" v-model="category" name="category"
                    class="mr-3 my-3  w-56 h-8 rounded-xl text-black">
                    <option class="text-black" v-for="category in categoris" :value="category.id" :key="category.id"
                        :class="[isWrongAmount == true ? 'border-red-600 border-2' : 'border-gray-600']">
                        {{ category.name }}</option>
                </select>
                <p v-if="isWrongCategory">
                    <b class="text-red-600 text-lg mr-5">لطفا دسته‌بندی را وارد کنید</b>
                </p>
            </div>
            <div class="inline-flex items-center flex-col">
                <label class="-mr-4">مبلغ</label>
                <div>
                    <input @change="isWrongAmount = false; isSuccess = false" v-model="amount" type="number"
                        :class="[isWrongAmount == true ? 'border-red-600 border-2' : 'border-gray-600']"
                        class="stylelists w-36" dir="ltr">
                    <div class="ml-4" dir="ltr"> {{ namayesheMablagh }}</div>

                </div>
            </div>
            <p class="mr-3 mt-8" v-if="isWrongAmount">
                <b class="text-red-600 text-lg">لطفا مبلغ را وارد کنید</b>
            </p>

            <div class="inline-flex justify-center mr-auto mt-7 ml-5">
                <button @click="newBudget()"
                    class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold">
                    ثبت
                </button>
            </div>
        </div>
    </div>
    <div class="relative">
        <div class=" absolute left-36 bottom-415">
            <h1 class="text-xl text-secondary-color">این بودجه برای ماه اسفند میباشد.</h1>
        </div>
    </div>
    <div v-if="isSuccess" class="text-secondary-color font-bold text-2xl -mt-48 text-center">
        عملیات با موفقیت انجام شد
    </div>
    <div v-if="isErrors" v-for="error in errors" class="text-secondary-color font-bold text-f20 mt-11 text-end ml-435">
        {{ error }}
    </div>
</template>
<script>
export default {
    data() {
        return {
            category: null,
            categoris: [],
            amount: null,
            isSuccess: false,
            isErrors: false,
            errors: [],
            isWrongAmount: false,
            isWrongCategory: false,
            mablaghBeFarsi: null
        }
    },
    methods: {
        getCategory() {
            fetch('http://5.42.94.18:3000/api/v1/category')
                .then(response => response.json())
                .then(response => {
                    this.categoris = response.data
                }
                )
        },
        newBudget() {
            if (this.amount == null || this.amount <= 0) {
                this.isWrongAmount = true
            }
            if (this.category == null) {
                this.isWrongCategory = true
            }
            if (this.isWrongAmount === true || this.isWrongCategory === true) {
                return
            }

            const requestOptions = {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ category_id: this.category, amount: this.amount, year: 1402, month: 12 })
            };
            fetch('http://5.42.94.18:3000/api/v1/wallet/2/budget', requestOptions)
                .then(response => {
                    if (response.status === 204) {
                        this.isSuccess = true
                        this.clearpage()
                    }
                    if (response.status === 422) {
                        response.json()
                            .then(khata => {
                                this.isErrors = true
                                this.errors = khata.errors
                            })
                    }
                })
        },
        clearpage() {
            this.amount = null
            this.category = null
        },

    },
    created() {
        this.getCategory()
    }
}
</script>
<style scoped>
.style {
    @apply mr-52 h-32 border rounded-xl -mt-96 px-1 pt-4 text-secondary-color flex flex-row
}

.stylelists {
    @apply flex items-center mr-14 border my-3 py-3 px-4 rounded-xl h-8 bg-white text-main-color
}
</style>
