<template>
    <h3>Add new transaction</h3>
        <form id="form" @submit.prevent="onSubmit">
            <div class="form-control">
                <label for="text">Text</label>
                <input type="text" id="text" v-model="text" placeholder="Enter text..." />
            </div>
            <div class="form-control">
                <label for="amount">
                    Amount <br />
                (negative - expense, positive - income)
                </label>
                <input type="text" id="amount" v-model="amount" @input="isValidNumber($event)" 
                @change="isValidInput($event)"
                placeholder="Enter amount..." />
            </div>
            <button class="btn">Add transaction</button>
        </form>
</template>

<script setup>
    import { ref } from 'vue';
    import { useToast } from 'vue-toastification';

    const text = ref('');
    const amount = ref('');

    const toast = useToast();

    const emit = defineEmits([
        'transactionSubmitted'
    ]);

    let lastValidAmountInput = '';

    const validNumber = /^-?(0|([1-9][0-9]{0,}))(\.\d{0,5})?$/;

    function isValidNumber(event) {
        
        if (event.target.value == '-') {lastValidAmountInput='-';return;}
        if (event.target.value == '') {lastValidAmountInput='';return;}
        if ( !validNumber.test(event.target.value) ) {
            event.target.value = lastValidAmountInput;
        } else {
            lastValidAmountInput = event.target.value;
        }
    };

    function isValidInput(event) {
        if ( !validNumber.test(event.target.value) ) {
            event.target.value = '';
        }
    }

    const onSubmit = () => {
        if (!text.value || !amount.value) {
            toast.error('Both fields must be filled');
            return;
        }
        
        const transactionData = {
            text: text.value,
            amount: parseFloat(amount.value)
        };

        emit('transactionSubmitted', transactionData);

        text.value = '';
        amount.value = '';
    }

    
</script>