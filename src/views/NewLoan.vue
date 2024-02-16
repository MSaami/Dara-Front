<template>
    <div>
        <div class="flex justify-center">
            <div class="style w-2/3">
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> تسهیلات </label>
                    <input @change="isWrongTitle = false; isSuccess = false" v-model="title" type="text"
                        class="stylelists w-44"
                        :class="[isWrongTitle == true ? 'border-red-600 border-2' : 'border-gray-600']">
                    <p v-if="isWrongTitle" class="mt-3 text-red-600 text-lg">
                        لطفا تسهیلات را وارد کنید
                    </p>
                </div>
                <div class="inline-flex items-start flex-col">
                    <div class="inline-flex">
                        <img @click="increseLoan()" class="w-6 h-6 ml-2 bg-white rounded-full" src="../assets/addd.png"
                            alt="add">
                        <label class="mb-3"> تعداد قسط </label>
                    </div>
                    <div v-for="ghest in installments" class="inline-flex mb-2"
                        @change="isWrongInstallment = false; isSuccess = false">
                        <input v-model="ghest.number_of_installment" type="number" class="stylelists w-9 ml-2"
                            :class="[isWrongInstallment == true ? 'border-red-600 border-2' : 'border-gray-600']">
                        <input v-model="ghest.amount" type="number" class="stylelists w-36"
                            :class="[isWrongInstallment == true ? 'border-red-600 border-2' : 'border-gray-600']">
                    </div>
                    <p v-if="isWrongInstallment" class="mt-3 text-red-600 text-lg">
                        لطفا تعداد اقساط را وارد کنید
                    </p>
                </div>
                <div class="inline-flex items-start flex-col">
                    <label class="mb-3"> تاریخ اولین قسط </label>
                    <date-picker @change="isWrongDate = false; isSuccess = false" format="YYYY-MM-DD" display-format="jYYYY-jMM-jDD" class="text-black rounded-xl" v-model="date"></date-picker>
                    <p v-if="isWrongDate" class="mt-3 text-red-600 text-lg">
                        لطفا تاریخ اولین قسط را وارد کنید
                    </p>
                </div>
                <div class="flex mt-auto mb-4">
                    <button @click="newLoan()"
                        class="h-9 border w-16 rounded-xl bg-secondary-color text-main-color font-bold ">
                        ثبت
                    </button>
                </div>
            </div>
        </div>
        <div v-if="isSuccess" class="text-secondary-color font-bold text-2xl -mt-32 mr-36 text-center">
            عملیات با موفقیت انجام شد
        </div>
        <div v-if="isErrors" v-for="error in errors" class="text-secondary-color font-bold text-f20 mt-11 text-end ml-435">
            {{ error }}
        </div>
    </div>
</template>
<script>
import Vue3PersianDatetimePicker from 'vue3-persian-datetime-picker'
export default {
    data() {
        return {
            title: null,
            date: '',
            isSuccess: false,
            installments: [{ number_of_installment: null, amount: null }],
            isErrors: false,
            errors: [],
            isWrongTitle: false,
            isWrongInstallment: false,
            isWrongDate: false,
        }
    },
    components: {
        DatePicker: Vue3PersianDatetimePicker
    },
    methods: {
        increseLoan() {
            this.installments.push({ number_of_installment: null, amount: null })
        },
        newLoan() {
            if (this.title === null) {
                this.isWrongTitle = true
            }
            if (this.date == '') {
                this.isWrongDate = true
            }

            this.installments.forEach(ghest => {
                if (ghest.number_of_installment == null || ghest.number_of_installment <= 0) {
                    this.isWrongInstallment = true
                }
                if (ghest.amount == null || ghest.amount <= 0) {
                    this.isWrongInstallment = true
                }
            })
            if (this.isWrongTitle === true || this.isWrongInstallment === true || this.isWrongDate === true) {
                return
            }
            const requestOptions = {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({
                    loan: {
                        title: this.title,
                        due_date: this.date,
                        installments: this.installments
                    }
                })
            };
            fetch('http://5.42.94.18:3000/api/v1/wallet/2/loan', requestOptions)
                .then(response => {
                    if (response.status === 204) {
                        this.isSuccess = true;
                        this.clearPage()
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
        clearPage() {
            this.loans = 0
            this.installments = []
            this.title = null
            this.date = ''
        },
    },
}
</script>
<style scoped>
.style {
    @apply h-48 mr-52 justify-evenly items-start pt-9 border rounded-xl text-secondary-color flex flex-row -mt-96
}

.stylelists {
    @apply h-8 flex items-center border rounded-xl text-main-color pr-3
}
</style>