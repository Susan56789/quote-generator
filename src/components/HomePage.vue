<template>
    <div class="container mx-auto p-4">
        <h1 class="text-2xl font-bold mb-4">Quote Generator</h1>

        <!-- Input Form -->
        <div class="mb-4 space-y-4">
            <div class="grid grid-cols-2 gap-4">
                <!-- Company Details -->
                <div>
                    <h2 class="text-xl font-semibold mb-2">Company Details</h2>
                    <input v-model="companyDetails.name" placeholder="Company Name" class="w-full border p-2">
                    <input v-model="companyDetails.address" placeholder="Company Address"
                        class="w-full border p-2 mt-2">
                    <input v-model="companyDetails.contact" placeholder="Company Contact"
                        class="w-full border p-2 mt-2">
                    <input v-model="companyDetails.email" placeholder="Company Email" class="w-full border p-2 mt-2">
                    <!-- Company Logo Upload -->
                    <input type="file" @change="handleLogoUpload" accept="image/*" class="w-full border p-2 mt-2">
                    <p v-if="companyDetails.logo">Logo uploaded: {{ companyDetails.logo.name }}</p>
                </div>

                <!-- Quote Details -->
                <div>
                    <h2 class="text-xl font-semibold mb-2">Quote Details</h2>
                    <input v-model="quoteData.title" placeholder="Quote Title" class="w-full border p-2">
                    <input v-model="quoteData.ref" placeholder="Reference Number" class="w-full border p-2">
                    <input v-model="quoteData.date" disabled type="date" class="w-full border p-2 mt-2">
                    <input v-model="quoteData.client" placeholder="Client Name" class="w-full border p-2 mt-2">
                </div>

                <!-- Terms -->
                <div>
                    <h2 class="text-xl font-semibold mb-2">Terms</h2>
                    <input v-model="quoteData.paymentTerms" placeholder="Payment Terms" class="w-full border p-2">
                    <input v-model="quoteData.supply" placeholder="Supply" class="w-full border p-2 mt-2">
                    <input v-model="quoteData.contactPerson" placeholder="Contact Person"
                        class="w-full border p-2 mt-2">
                    <input v-model="quoteData.warranty" placeholder="Warranty" class="w-full border p-2 mt-2">
                </div>
            </div>

            <!-- Products -->
            <div>
                <h2 class="text-xl font-semibold mb-2">Products</h2>
                <div v-for="(product, index) in quoteData.products" :key="index" class="flex mb-2 space-x-2">
                    <input v-model="product.particulars" placeholder="Particulars" class="flex-grow border p-2">
                    <input v-model.number="product.units" type="number" placeholder="Units" class="w-24 border p-2">
                    <input v-model.number="product.unitPrice" type="number" placeholder="Unit Price"
                        class="w-32 border p-2">
                    <button @click="removeProduct(index)"
                        class="bg-red-500 text-white px-4 py-2 rounded">Remove</button>
                </div>
                <button @click="addProduct" class="bg-green-500 text-white px-4 py-2 rounded mt-2">Add Product</button>
            </div>
        </div>

        <button @click="generateQuote" class="bg-blue-500 text-white px-4 py-2 rounded">Generate Quote</button>

        <!-- Generated Quote -->
        <div v-if="showQuote" class="mt-8 border p-4">
            <!-- Header Layout for Company and Client -->
            <div class="flex justify-between">
                <div>
                    <img :src="companyLogoUrl" alt="Company Logo" class="h-24">
                    <h2 class="text-lg font-bold mt-2">{{ companyDetails.name }}</h2>
                    <p>{{ companyDetails.address }}</p>
                    <p>{{ companyDetails.contact }}</p>
                    <p>{{ companyDetails.email }}</p>
                </div>
                <div class="text-right">
                    <p><strong>Ref:</strong> {{ quoteData.ref }}</p>
                    <p><strong>Date:</strong> {{ formatDate(quoteData.date) }}</p>
                    <p><strong>Client:</strong> {{ quoteData.client }}</p>
                </div>
            </div>

            <h2 class="text-center font-bold text-2xl mt-4 underline">{{ quoteData.title }}</h2>

            <!-- Quote Information -->
            <table class="w-full border mb-4 mt-4">
                <tr>
                    <th class="border p-2 text-left">Payment Terms (Goods)</th>
                    <th class="border p-2 text-left">Supply</th>
                    <th class="border p-2 text-left">Contact Person</th>
                    <th class="border p-2 text-left">Warranty (Products)</th>
                </tr>
                <tr>
                    <td class="border p-2">{{ quoteData.paymentTerms }}</td>
                    <td class="border p-2">{{ quoteData.supply }}</td>
                    <td class="border p-2">{{ quoteData.contactPerson }}</td>
                    <td class="border p-2">{{ quoteData.warranty }}</td>
                </tr>
            </table>

            <!-- Product Information -->
            <table class="w-full border">
                <tr>
                    <th class="border p-2">No.</th>
                    <th class="border p-2">Particulars</th>
                    <th class="border p-2">Units</th>
                    <th class="border p-2">Unit Price (Ksh)</th>
                    <th class="border p-2">Total (Ksh)</th>
                </tr>
                <tr v-for="(product, index) in quoteData.products" :key="index">
                    <td class="border p-2">{{ index + 1 }}</td>
                    <td class="border p-2">{{ product.particulars }}</td>
                    <td class="border p-2">{{ product.units }}</td>
                    <td class="border p-2">{{ formatCurrency(product.unitPrice) }}</td>
                    <td class="border p-2">{{ formatCurrency(calculateTotal(product)) }}</td>
                </tr>
                <tr>
                    <td colspan="4" class="border p-2 text-right font-bold">Sub-total</td>
                    <td class="border p-2">{{ formatCurrency(subTotal) }}</td>
                </tr>
                <tr>
                    <td colspan="4" class="border p-2 text-right font-bold">VAT 16%</td>
                    <td class="border p-2">{{ formatCurrency(vat) }}</td>
                </tr>
                <tr>
                    <td colspan="4" class="border p-2 text-right font-bold">Grand total cost</td>
                    <td class="border p-2 font-bold">{{ formatCurrency(grandTotal) }}</td>
                </tr>
            </table>

            <!-- Terms and Conditions -->
            <div class="mt-4">
                <p>Terms: 100% payment on confirmation of order with LPO issuance.</p>
                <p>For goods / materials, delivery: 14 working days ex-stock from the date of placement of order;
                    otherwise, out-of-stock items will take 4 weeks.</p>
            </div>

            <!-- Customer Acceptance -->
            <div class="mt-4">
                <h3 class="font-bold">Customer acceptance</h3>
                <p>Please sign acceptance of the quotation over a company rubber stamp.</p>
            </div>

            <!-- Signature Section -->
            <div class="mt-4 flex justify-between items-end">
                <div>
                    <p>Signature: _________________</p>
                    <p>{{ companyDetails.name }}</p>
                </div>
                <div>
                    <p>Signature: _________________</p>
                    <p>Date: _________________</p>
                </div>
            </div>

            <button @click="printQuote" class="mt-4 bg-gray-500 text-white px-4 py-2 rounded">Print Quote</button>
        </div>
    </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

const currentDate = new Date().toISOString().split('T')[0];
const generateRef = () => {
    const year = new Date().getFullYear();
    const randomNumber = Math.floor(100000 + Math.random() * 900000);
    return `IFS/${year}/${randomNumber}`;
};

const companyDetails = reactive({
    name: '',
    address: '',
    contact: '',
    email: '',
    logo: null
});

const quoteData = reactive({
    ref: generateRef(),
    date: currentDate,
    client: '',
    paymentTerms: '',
    supply: '',
    contactPerson: '',
    warranty: '',
    products: [],
    title: '',
});

const addProduct = () => {
    quoteData.products.push({
        particulars: '',
        units: 0,
        unitPrice: 0
    });
};

const removeProduct = (index) => {
    quoteData.products.splice(index, 1);
};

const calculateTotal = (product) => {
    return product.units * product.unitPrice;
};

const subTotal = computed(() => {
    return quoteData.products.reduce((total, product) => total + calculateTotal(product), 0);
});

const vat = computed(() => {
    return subTotal.value * 0.16;
});

const grandTotal = computed(() => {
    return subTotal.value + vat.value;
});

const formatCurrency = (amount) => {
    return new Intl.NumberFormat('en-KE', {
        style: 'currency',
        currency: 'KES'
    }).format(amount);
};

const showQuote = ref(false);

const generateQuote = () => {
    showQuote.value = true;
};

const printQuote = () => {
    window.print();
};

const companyLogoUrl = ref(null);

const handleLogoUpload = (event) => {
    const file = event.target.files[0];
    if (file) {
        companyDetails.logo = file;
        companyLogoUrl.value = URL.createObjectURL(file);
    }
};

const formatDate = (dateString) => {
    const options = { year: 'numeric', month: 'long', day: 'numeric' };
    const dateObj = new Date(dateString);
    return dateObj.toLocaleDateString(undefined, options);
};
</script>

<style scoped>
/* Additional styling for better UI */
.container {
    max-width: 800px;
}
</style>
