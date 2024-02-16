<template>
    <div class="flex justify-center">
        <div class="style">
            <div class="inline-flex items-center">
                <label class="ml-6">دسته‌بندی <span class="text-red-600 text-2xl">*</span></label>
                <select v-model="dastebandi" name="category" class="-mr-1 my-3 px-3 w-56 h-8 rounded-xl text-black">
                    <option v-for="category in categoris" :value="category.id" class="text-black">
                        {{ category.name }}</option>
                </select>
                <p v-if="isWrongCategory">
                    <b class="text-red-600 text-lg mr-5">لطفا دسته‌بندی را وارد کنید</b>
                </p>
            </div>
            <div class="inline-flex items-center">
                <label>نوع تراکنش <span class="text-red-600 text-2xl">*</span></label>
                <select v-model="transactionType" class="mr-3 my-3 px-4 rounded-xl h-8 w-56 text-black">
                    <option value="depoist">واریز</option>
                    <option value="withdraw">برداشت</option>
                </select>
            </div>
            <div class="inline-flex items-center">
                <label>مبلغ <span class="text-red-600 text-2xl">*</span></label>
                <div class="flex flex-col">
                    <input type="number" dir="ltr" v-model="mablagh" @change="isWrongAmount = false"
                        :class="[isWrongAmount == true ? 'border-red-600 border-2' : 'border-gray-600']"
                        class="mr-14 border my-3 py-3 px-4 rounded-xl h-8 text-slate-950 w-56">
                    <div class="ml-4" dir="ltr" v-if="mablagh > 0">{{ mablagh.toLocaleString() }}</div>
                </div>
                <p v-if="isWrongAmount">
                    <b class="text-red-600 text-lg mr-5">لطفا مبلغ را وارد کنید</b>
                </p>
            </div>
            <div class="inline-flex items-start">
                <label class="mt-3">توضیحات</label>
                <textarea v-model="tozihat" class="mr-9 border my-3 py-3 px-4 rounded-xl h-52 text-slate-950 w-56"
                    type="text" cols="23" rows="5"></textarea>
            </div>
            <div class="inline-flex justify-end">
                <button @click="editData()"
                    class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold -mt-7">
                    ویرایش
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
</template>
<script>
export default {
    data() {
        return {
            categoris: [],
            mablagh: null,
            tozihat: null,
            dastebandi: null,
            transactionType: null,
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
            fetch('http://5.42.94.18:3000/api/v1/wallet_transaction/' + this.$route.params.transactionId)
                .then(response => response.text())
                .then(response => JSON.parse(response))
                .then(response => {
                    this.tozihat = response.data.description
                    this.mablagh = response.data.amount
                    this.dastebandi = response.data.category_id
                    this.selectTransactionType()
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
        selectTransactionType() {
            if (this.mablagh <= 0) {
                this.transactionType = 'withdraw'
                this.mablagh = -this.mablagh

            } else {
                this.transactionType = 'depoist'
            }
        },
        editData() {
            let amountBackend = this.mablagh
            if (this.mablagh === null || this.mablagh <= 0) {
                this.isWrongAmount = true
            }
            if (this.dastebandi === null) {
                this.isWrongCategory = true
            }
            if (this.isWrongAmount === true || this.isWrongCategory === true) {
                return
            }
            if (this.transactionType === 'withdraw' && this.mablagh != null) {
                amountBackend = -this.mablagh
            }
            const requestOptions = {
                method: "PUT",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ amount: amountBackend, description: this.tozihat, category_id: this.dastebandi })
            };
            fetch('http://5.42.94.18:3000/api/v1/wallet_transaction/' + this.$route.params.transactionId, requestOptions)
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
        },
    },
    created() {
        this.getCategory()
        this.getData()

    }
}
</script>
<style scoped>
.style {
    @apply bg-main-color w-1/2 h-500 border rounded-xl -mt-435 px-6 pt-8 text-secondary-color flex flex-col
}

.stylelists {
    @apply border my-3 py-3 px-4 rounded-full inline-flex justify-between
}
</style>