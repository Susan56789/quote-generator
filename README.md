Certainly! Here is a single, formatted document you can use as a README file:

---

# Quote Generator

## Overview

The Quote Generator is a Vue.js application designed to create and manage quotes efficiently. It allows users to input company details, quote details, and product information. The generated quote can be reviewed, printed, or saved as needed.

## Features

- **Dynamic Quote Generation:** Automatically generates reference numbers and current dates.
- **Editable Fields:** Users can input and modify company details, quote details, and product information.
- **Product Management:** Add or remove products dynamically with automatic total calculations.
- **Quote Display:** View the generated quote with all necessary details and terms.
- **Print Functionality:** Print the quote directly from the application.

## Installation

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/quote-generator.git
   ```

2. **Navigate to the Project Directory**

   ```bash
   cd quote-generator
   ```

3. **Install Dependencies**

   Make sure you have [Node.js](https://nodejs.org/) and [npm](https://www.npmjs.com/) installed. Then, run:

   ```bash
   npm install
   ```

4. **Start the Development Server**

   ```bash
   npm run serve
   ```

   Your application should now be running at `http://localhost:8080`.

## Usage

1. **Fill Out Company Details:** Input the company's name, address, contact information, and email.

2. **Enter Quote Details:** Provide the reference number (auto-generated), date (auto-generated), and client name. The reference number and date fields are disabled and auto-generated.

3. **Specify Terms:** Fill in the payment terms, supply details, contact person, and warranty information.

4. **Manage Products:**

   - **Add Products:** Click "Add Product" to include a new product entry.
   - **Remove Products:** Click "Remove" next to a product to delete it.

5. **Generate Quote:** Click the "Generate Quote" button to display the quote with all details.

6. **Print Quote:** Use the "Print Quote" button to print the generated quote.

## File Structure

- `src/`
  - `components/` - Contains Vue components (not applicable in this setup)
  - `App.vue` - Main Vue component
  - `main.js` - Vue entry file
- `public/` - Static assets
- `package.json` - Project metadata and dependencies
- `README.md` - This file

## Vue Component Details

- **`<template>`**: Contains the HTML structure of the application.
- **`<script setup>`**: Vue 3 Composition API setup with reactive state management, computed properties, and methods.
- **`<style>`**: CSS styles, including print-specific styles.

## Computed Properties and Methods

- **`subTotal`**: Computes the subtotal of all products.
- **`vat`**: Calculates 16% VAT on the subtotal.
- **`grandTotal`**: Computes the grand total including VAT.
- **`formatCurrency(value)`**: Formats numbers as Kenyan Shillings.
- **`formatDate(dateString)`**: Formats date strings into a human-readable format.

## Print Styles

- **`@media print`**: Hides buttons and applies other print-specific styles.

## Contributing

If you wish to contribute to this project, please fork the repository, make your changes, and submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).
