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
                </div>

                <!-- Quote Details -->
                <div>
                    <h2 class="text-xl font-semibold mb-2">Quote Details</h2>
                    <input v-model="quoteData.ref" disabled placeholder="Reference Number" class="w-full border p-2">
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
            <div class="mb-4">
                <p><strong>Ref:</strong> {{ quoteData.ref }}</p>
                <p><strong>Date:</strong> {{ formatDate(quoteData.date) }}</p>
                <p><strong>Company:</strong> {{ companyDetails.name }}</p>
                <p><strong>Address:</strong> {{ companyDetails.address }}</p>
                <p><strong>Contact:</strong> {{ companyDetails.contact }}</p>
                <p><strong>Email:</strong> {{ companyDetails.email }}</p>
            </div>

            <!-- Client and Quote Information -->
            <table class="w-full border mb-4">
                <tr>
                    <th class="border p-2 text-left">Client</th>
                    <td class="border p-2">{{ quoteData.client }}</td>
                </tr>
            </table>

            <table class="w-full border mb-4">
                <tr>
                    <th class="border p-2">Payment terms (goods)</th>
                    <th class="border p-2">Supply</th>
                    <th class="border p-2">Contact person</th>
                    <th class="border p-2">Warranty (products)</th>
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
                    <th class="border p-2">Unit price (Ksh)</th>
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

            <div class="mt-4">
                <h3 class="font-bold">Cost variations:</h3>
                <p>Quotations and contracts are based on prevailing costs of production and are subject to amendment at
                    any time after acceptance to meet any rise in such costs.</p>
                <p>Quotation valid for 30 days.</p>
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

// Auto-generate the current date and a reference number
const currentDate = new Date().toISOString().split('T')[0]; // format as YYYY-MM-DD
const generateRef = () => {
    const year = new Date().getFullYear();
    const randomNumber = Math.floor(100000 + Math.random() * 900000); // generates a random six-digit number
    return `IFS/${year}/${randomNumber}`;
};

const companyDetails = reactive({
    name: '',
    address: '',
    contact: '',
    email: ''
});

const quoteData = reactive({
    ref: generateRef(),
    date: currentDate,
    client: '',
    paymentTerms: '100% advance',
    supply: '1-3 days',
    contactPerson: 'Susan - 0794 764 203',
    warranty: '1 year warranty',
    products: [
        { particulars: 'Supply of Tenda F3 N300 300Mbps Wireless Router', units: 1, unitPrice: 1350 }
    ]
});

const showQuote = ref(false);

const addProduct = () => {
    quoteData.products.push({ particulars: '', units: 0, unitPrice: 0 });
};

const removeProduct = (index) => {
    quoteData.products.splice(index, 1);
};

const generateQuote = () => {
    showQuote.value = true;
};

const printQuote = () => {
    window.print();
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

const formatCurrency = (value) => {
    return new Intl.NumberFormat('en-KE', { style: 'currency', currency: 'KES', minimumFractionDigits: 2 }).format(value);
};

const formatDate = (dateString) => {
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return new Date(dateString).toLocaleDateString('en-US', options);
};
</script>

<style>
@media print {
    button {
        display: none;
    }

    /* Add any other print-specific styles here */
}
</style>
