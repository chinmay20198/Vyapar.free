
The user wants a full billing and inventory software inspired by Vyapar.com that works entirely client-side in the browser. It needs to support invoices with GST tax, product and customer management, dashboard summary, sales reports, and language toggle between English and Hindi. It needs to be offline-ready by storing all data in localStorage. The app must be responsive for mobile and desktop, with a modern look and mobile controls optimized to fit a max of 600px height and 350px width, and be stable and production-ready.

I will deliver this as a single HTML file with all CSS and JS inlined to simplify deployment and usage, following the instructions.

Plan:
- Create a single `index.html` file containing:
  - Layout with sections/tabs/navigation for Products, Customers, Invoices, Dashboard/Reports, and Language toggle.
  - Product management: code, name, price, stock.
  - Customer management: name, phone, GST number.
  - Invoice creation with GST tax (CGST+SGST at a typical rate e.g. 9% each), product selection, quantity, price, calculate totals with taxes.
  - Dashboard summary with total sales, products, customers.
  - Sales report listing invoices with details.
  - Language toggle between English and Hindi, UI translations for main labels and messages.
  - Store all data (products, customers, invoices) in localStorage to persist.
  - Responsive, modern styling with cool fonts, colors, and layout optimized to fit mobile screen size constraints (no scroll on 600px height / 350px width).
- Use JavaScript for all functional logic including language toggling, data management, and UI interactions.
- Use open stock photos relevant to billing/business for decoration if images used.
- Ensure mobile-friendly form elements and buttons sized for touch.

I will now proceed to build the full app as a single HTML file implementing the above requirements.


`index.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Vyapar Free - Billing & Inventory Software</title>
<style>
  /* Reset & base */
  * {
    box-sizing: border-box;
  }
  body {
    margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #f7f9fc;
    color: #222;
    -webkit-user-select: none; user-select: none;
    -webkit-touch-callout: none;
  }
  h1, h2, h3, h4, h5 {
    margin: 0;
    font-weight: 600;
  }
  button {
    cursor: pointer;
    font-family: inherit;
  }
  a {
    color: inherit;
    text-decoration: none;
  }
  input, select, textarea {
    font-family: inherit;
    font-size: 1rem;
    padding: 8px 10px;
    border: 1px solid #bbb;
    border-radius: 4px;
    outline-offset: 2px;
    outline-color: transparent;
    transition: outline-color 0.15s ease;
  }
  input:focus, select:focus, textarea:focus {
    outline-color: #357edd;
    border-color: #357edd;
  }
  .sr-only {
    position: absolute;
    width: 1px; height: 1px; padding: 0; margin: -1px;
    overflow: hidden; clip: rect(0,0,0,0); border: 0;
  }

  /* Container setup */
  #app {
    max-width: 360px; /* slightly bigger than 350 to avoid clipping */
    height: 600px;
    margin: 0 auto;
    background: white;
    box-shadow: 0 0 18px rgba(0,0,0,0.1);
    display: flex;
    flex-direction: column;
    overflow: hidden;
    border-radius: 8px;
    user-select: none;
  }

  header {
    background: linear-gradient(90deg, #357edd, #285ebb);
    color: white;
    padding: 12px 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  header h1 {
    font-size: 1.25rem;
    font-weight: 700;
  }
  #lang-toggle {
    background: transparent;
    border: none;
    color: white;
    font-weight: 600;
    font-size: 1rem;
    padding: 6px 10px;
    border-radius: 4px;
    transition: background-color 0.3s ease;
  }
  #lang-toggle:hover {
    background-color: rgba(255,255,255,0.2);
  }

  nav {
    display: flex;
    justify-content: space-around;
    border-top: 1px solid #eaeaea;
    border-bottom: 1px solid #eaeaea;
    background: #fafafa;
    user-select:none;
  }
  nav button {
    flex-grow: 1;
    border: none;
    background: transparent;
    padding: 10px 0;
    font-weight: 600;
    color: #555;
    font-size: 0.9rem;
    transition: background-color 0.3s ease, color 0.3s ease;
  }
  nav button.active {
    color: #357edd;
    border-bottom: 3px solid #357edd;
    background: #e6f0ff;
  }
  nav button:focus {
    outline: none;
    background: #dde9ff;
  }

  main {
    flex-grow: 1;
    overflow-y: auto;
    padding: 12px 16px;
    -webkit-overflow-scrolling: touch;
  }
  section {
    display: none;
    height: 100%;
  }
  section.active {
    display: block;
  }

  /* Common form area */
  form {
    max-width: 100%;
  }
  label {
    display: block;
    margin-bottom: 6px;
    font-weight: 600;
  }
  .form-group {
    margin-bottom: 14px;
  }

  /* Buttons */
  button.primary {
    background-color: #357edd;
    color: white;
    border: none;
    padding: 10px 14px;
    font-weight: 700;
    border-radius: 5px;
    box-shadow: 0 2px 6px rgba(53,126,221,0.5);
    width: 100%;
    user-select:none;
  }
  button.primary:hover {
    background-color: #285ebb;
  }
  button.secondary {
    background-color: #ebebeb;
    border: none;
    color: #555;
    padding: 10px 14px;
    border-radius: 5px;
    width: 100%;
    font-weight: 600;
    user-select:none;
  }
  button.secondary:hover {
    background-color: #d4d4d4;
  }
  button.danger {
    background-color: #d00000;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 5px;
    font-weight: 700;
    cursor: pointer;
    user-select:none;
  }
  button.danger:hover {
    background-color: #9b0000;
  }
  .btn-inline {
    display: inline-block;
    margin-right: 8px;
  }

  /* Tables */
  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.9rem;
  }
  th, td {
    padding: 6px 8px;
    border: 1px solid #ddd;
    text-align: left;
  }
  th {
    background-color: #357edd;
    color: white;
    font-weight: 600;
    font-size: 0.85rem;
    user-select:none;
  }
  td.numeric {
    text-align: right;
  }

  /* Scroll small height */
  main::-webkit-scrollbar {
    width: 6px;
  }
  main::-webkit-scrollbar-thumb {
    background: #357eddcc;
    border-radius: 3px;
  }
  main::-webkit-scrollbar-track {
    background: transparent;
  }

  /* Responsive & mobile fit */
  @media (max-width: 400px) {
    #app {
      width: 100vw;
      height: 100vh;
      border-radius: 0;
      max-width: none;
      max-height: none;
    }
    main {
      padding: 10px;
    }
  }

  /* Notification/toast */
  #toast {
    position: fixed;
    bottom: 12px;
    left: 50%;
    transform: translateX(-50%);
    background: #357edd;
    color: white;
    padding: 10px 18px;
    border-radius: 6px;
    font-weight: 600;
    box-shadow: 0 2px 6px rgba(53,126,221,0.6);
    opacity: 0;
    pointer-events:none;
    transition: opacity 0.3s ease;
    max-width: 90vw;
    z-index: 1000;
    user-select:none;
  }
  #toast.show {
    opacity: 1;
    pointer-events:auto;
  }

</style>
</head>
<body>
  <div id="app" role="main" aria-label="Vyapar Free Billing and Inventory Software">
    <header>
      <h1 id="app-title">Vyapar Free</h1>
      <button id="lang-toggle" aria-label="Toggle Language" title="Toggle Language">हिंदी</button>
    </header>
    <nav role="navigation" aria-label="Primary Navigation">
      <button data-section="dashboard" class="active" aria-current="page" type="button">Dashboard</button>
      <button data-section="products" type="button">Products</button>
      <button data-section="customers" type="button">Customers</button>
      <button data-section="invoices" type="button">Invoices</button>
      <button data-section="reports" type="button">Reports</button>
    </nav>

    <main>
      <!-- Dashboard -->
      <section id="dashboard" class="active" aria-labelledby="dashboard-title" tabindex="0">
        <h2 id="dashboard-title">Dashboard Summary</h2>
        <div>
          <p><strong id="total-products">0</strong> Products</p>
          <p><strong id="total-customers">0</strong> Customers</p>
          <p><strong id="total-invoices">0</strong> Invoices</p>
          <p><strong id="total-sales">₹0.00</strong> Total Sales</p>
        </div>
      </section>
      
      <!-- Products -->
      <section id="products" aria-labelledby="products-title" tabindex="0">
        <h2 id="products-title">Products Management</h2>
        <form id="product-form" aria-label="Add or edit product form">
          <div class="form-group">
            <label for="product-code">Product Code</label>
            <input type="text" id="product-code" name="productCode" required maxlength="20" autocomplete="off" placeholder="e.g. P001" />
          </div>
          <div class="form-group">
            <label for="product-name">Name</label>
            <input type="text" id="product-name" name="productName" required maxlength="50" autocomplete="off" placeholder="Product name" />
          </div>
          <div class="form-group">
            <label for="product-price">Price (₹)</label>
            <input type="number" id="product-price" name="productPrice" required min="0" step="0.01" placeholder="Unit price" />
          </div>
          <div class="form-group">
            <label for="product-stock">Stock Quantity</label>
            <input type="number" id="product-stock" name="productStock" required min="0" step="1" placeholder="Available stock" />
          </div>
          <button type="submit" class="primary" id="product-submit">Add Product</button>
          <button type="button" class="secondary" id="product-cancel" style="display:none;">Cancel</button>
        </form>
        <hr />
        <h3>Products List</h3>
        <table aria-label="Products list" id="products-table">
          <thead>
            <tr><th>Code</th><th>Name</th><th>Price (₹)</th><th>Stock</th><th>Actions</th></tr>
          </thead>
          <tbody></tbody>
        </table>
      </section>
      
      <!-- Customers -->
      <section id="customers" aria-labelledby="customers-title" tabindex="0">
        <h2 id="customers-title">Customers Management</h2>
        <form id="customer-form" aria-label="Add or edit customer form">
          <div class="form-group">
            <label for="customer-name">Customer Name</label>
            <input type="text" id="customer-name" name="customerName" required maxlength="50" autocomplete="off" placeholder="Customer name" />
          </div>
          <div class="form-group">
            <label for="customer-phone">Phone Number</label>
            <input type="tel" id="customer-phone" name="customerPhone" maxlength="15" pattern="[0-9+- ]*" placeholder="Phone or mobile number" autocomplete="off" />
          </div>
          <div class="form-group">
            <label for="customer-gst">GST Number</label>
            <input type="text" id="customer-gst" name="customerGST" maxlength="15" autocomplete="off" placeholder="GSTIN" />
          </div>
          <button type="submit" class="primary" id="customer-submit">Add Customer</button>
          <button type="button" class="secondary" id="customer-cancel" style="display:none;">Cancel</button>
        </form>
        <hr />
        <h3>Customers List</h3>
        <table aria-label="Customers list" id="customers-table">
          <thead>
            <tr><th>Name</th><th>Phone</th><th>GST Number</th><th>Actions</th></tr>
          </thead>
          <tbody></tbody>
        </table>
      </section>

      <!-- Invoices -->
      <section id="invoices" aria-labelledby="invoices-title" tabindex="0">
        <h2 id="invoices-title">Create Invoice</h2>
        <form id="invoice-form" aria-label="Create invoice form">
          <div class="form-group">
            <label for="invoice-customer">Select Customer</label>
            <select id="invoice-customer" name="invoiceCustomer" required>
              <option value="" disabled selected>Select customer</option>
            </select>
          </div>
          <div class="form-group">
            <label>Invoice Items</label>
            <table id="invoice-items-table" aria-label="Invoice items">
              <thead>
                <tr>
                  <th>Product</th><th>Price (₹)</th><th>Qty</th><th>Total (₹)</th><th></th>
                </tr>
              </thead>
              <tbody>
                <!-- Items added dynamically -->
              </tbody>
              <tfoot>
                <tr>
                  <td colspan="5">
                    <button type="button" class="secondary" id="add-invoice-item">+ Add Item</button>
                  </td>
                </tr>
              </tfoot>
            </table>
          </div>
          <hr />
          <div>
            <p>Sub Total: ₹<span id="invoice-subtotal">0.00</span></p>
            <p>CGST (9%): ₹<span id="invoice-cgst">0.00</span></p>
            <p>SGST (9%): ₹<span id="invoice-sgst">0.00</span></p>
            <p><strong>Total: ₹<span id="invoice-total">0.00</span></strong></p>
          </div>
          <button type="submit" class="primary" id="invoice-submit">Save Invoice</button>
        </form>
        <hr />
        <h3>Invoices List</h3>
        <table aria-label="Invoices list" id="invoices-table">
          <thead>
            <tr><th>#</th><th>Customer</th><th>Date</th><th>Total (₹)</th><th>Actions</th></tr>
          </thead>
          <tbody></tbody>
        </table>
      </section>

      <!-- Reports -->
      <section id="reports" aria-labelledby="reports-title" tabindex="0">
        <h2 id="reports-title">Sales Reports</h2>
        <table aria-label="Sales report" id="reports-table">
          <thead>
            <tr><th>#</th><th>Date</th><th>Customer</th><th>Invoice Total (₹)</th></tr>
          </thead>
          <tbody></tbody>
        </table>
      </section>
    </main>
  </div>

  <div id="toast" role="alert" aria-live="assertive" aria-atomic="true"></div>

<script>
(() => {
  'use strict';

  // Constants
  const CGST_RATE = 0.09;
  const SGST_RATE = 0.09;

  // UI Elements
  const navButtons = document.querySelectorAll('nav button');
  const sections = document.querySelectorAll('main > section');
  const langToggleBtn = document.getElementById('lang-toggle');
  const toastEl = document.getElementById('toast');

  // Storage keys
  const PRODUCTS_KEY = 'vyapar_products';
  const CUSTOMERS_KEY = 'vyapar_customers';
  const INVOICES_KEY = 'vyapar_invoices';
  const LANG_KEY = 'vyapar_language';

  // State
  let products = [];
  let customers = [];
  let invoices = [];
  let language = 'en'; // 'en' or 'hi'

  // Translation dictionary for main UI labels
  const translations = {
    en: {
      'Vyapar Free': 'Vyapar Free',
      Dashboard: 'Dashboard',
      Products: 'Products',
      Customers: 'Customers',
      Invoices: 'Invoices',
      Reports: 'Reports',
      'Dashboard Summary': 'Dashboard Summary',
      'Products Management': 'Products Management',
      'Customers Management': 'Customers Management',
      'Create Invoice': 'Create Invoice',
      'Sales Reports': 'Sales Reports',
      'Add Product': 'Add Product',
      'Edit Product': 'Edit Product',
      'Add Customer': 'Add Customer',
      'Edit Customer': 'Edit Customer',
      'Select Customer': 'Select Customer',
      'Invoice Items': 'Invoice Items',
      'Add Item': '+ Add Item',
      'Sub Total': 'Sub Total',
      'CGST (9%)': 'CGST (9%)',
      'SGST (9%)': 'SGST (9%)',
      'Total': 'Total',
      'Save Invoice': 'Save Invoice',
      'Products List': 'Products List',
      'Customers List': 'Customers List',
      'Invoices List': 'Invoices List',
      'Sales Reports': 'Sales Reports',
      'Product Code': 'Product Code',
      'Name': 'Name',
      'Price (₹)': 'Price (₹)',
      Stock: 'Stock',
      'Stock Quantity': 'Stock Quantity',
      'Customer Name': 'Customer Name',
      'Phone Number': 'Phone Number',
      'GST Number': 'GST Number',
      'Actions': 'Actions',
      'Cancel': 'Cancel',
      'Delete': 'Delete',
      'Edit': 'Edit',
      'Select product': 'Select product',
      'Invoice #': 'Invoice #',
      'Date': 'Date',
      'Customer': 'Customer',
      'Total Sales': 'Total Sales',
      'Total': 'Total',
      'Product name': 'Product name',
      'Phone or mobile number': 'Phone or mobile number',
      'GSTIN': 'GSTIN',
      'Toggle Language': 'Toggle Language',
      'Hindi': 'हिंदी',
      'English': 'English',
      'Please select a customer': 'Please select a customer',
      'Please add products before creating invoice': 'Please add products before creating invoice',
      'Please fill all fields correctly': 'Please fill all fields correctly',
      'Item removed': 'Item removed',
      'Product added': 'Product added',
      'Product updated': 'Product updated',
      'Product deleted': 'Product deleted',
      'Customer added': 'Customer added',
      'Customer updated': 'Customer updated',
      'Customer deleted': 'Customer deleted',
      'Invoice saved': 'Invoice saved',
      'No data available': 'No data available',
    },
    hi: {
      'Vyapar Free': 'व्यापार फ्री',
      Dashboard: 'डैशबोर्ड',
      Products: 'उत्पाद',
      Customers: 'ग्राहक',
      Invoices: 'चालान',
      Reports: 'रिपोर्ट्स',
      'Dashboard Summary': 'डैशबोर्ड सारांश',
      'Products Management': 'उत्पाद प्रबंधन',
      'Customers Management': 'ग्राहक प्रबंधन',
      'Create Invoice': 'चालान बनाएं',
      'Sales Reports': 'बिक्री रिपोर्ट',
      'Add Product': 'उत्पाद जोड़ें',
      'Edit Product': 'उत्पाद संपादित करें',
      'Add Customer': 'ग्राहक जोड़ें',
      'Edit Customer': 'ग्राहक संपादित करें',
      'Select Customer': 'ग्राहक चुनें',
      'Invoice Items': 'चालान आइटम',
      'Add Item': '+ आइटम जोड़ें',
      'Sub Total': 'उप कुल',
      'CGST (9%)': 'सीजीएसटी (9%)',
      'SGST (9%)': 'एसजीएसटी (9%)',
      'Total': 'कुल',
      'Save Invoice': 'चालान सुरक्षित करें',
      'Products List': 'उत्पाद सूची',
      'Customers List': 'ग्राहक सूची',
      'Invoices List': 'चालान सूची',
      'Sales Reports': 'बिक्री रिपोर्ट',
      'Product Code': 'उत्पाद कोड',
      'Name': 'नाम',
      'Price (₹)': 'मूल्य (₹)',
      Stock: 'स्टॉक',
      'Stock Quantity': 'स्टॉक मात्रा',
      'Customer Name': 'ग्राहक नाम',
      'Phone Number': 'फोन नंबर',
      'GST Number': 'जीएसटी नंबर',
      'Actions': 'कार्य',
      'Cancel': 'रद्द करें',
      'Delete': 'हटाएं',
      'Edit': 'संपादित करें',
      'Select product': 'उत्पाद चुनें',
      'Invoice #': 'चालान #',
      'Date': 'तारीख',
      'Customer': 'ग्राहक',
      'Total Sales': 'कुल बिक्री',
      'Total': 'कुल',
      'Product name': 'उत्पाद का नाम',
      'Phone or mobile number': 'फोन या मोबाइल नंबर',
      'GSTIN': 'जीएसटीआईएन',
      'Toggle Language': 'भाषा बदलें',
      'Hindi': 'हिंदी',
      'English': 'अंग्रेज़ी',
      'Please select a customer': 'कृपया ग्राहक चुनें',
      'Please add products before creating invoice': 'चालान बनाने से पहले उत्पाद जोड़ें',
      'Please fill all fields correctly': 'कृपया सभी फ़ील्ड सही भरें',
      'Item removed': 'आइटम हटाया गया',
      'Product added': 'उत्पाद जोड़ा गया',
      'Product updated': 'उत्पाद अपडेट किया गया',
      'Product deleted': 'उत्पाद हटाया गया',
      'Customer added': 'ग्राहक जोड़ा गया',
      'Customer updated': 'ग्राहक अपडेट किया गया',
      'Customer deleted': 'ग्राहक हटाया गया',
      'Invoice saved': 'चालान सुरक्षित किया गया',
      'No data available': 'डेटा उपलब्ध नहीं है',
    }
  };

  // Utility translation function
  function t(key) {
    return translations[language][key] || key;
  }

  // Show toast message
  function showToast(msg, duration=3000) {
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastEl.dismissTimeout);
    toastEl.dismissTimeout = setTimeout(() => {
      toastEl.classList.remove('show');
    }, duration);
  }

  // Save and Load from localStorage
  function saveData() {
    localStorage.setItem(PRODUCTS_KEY, JSON.stringify(products));
    localStorage.setItem(CUSTOMERS_KEY, JSON.stringify(customers));
    localStorage.setItem(INVOICES_KEY, JSON.stringify(invoices));
    localStorage.setItem(LANG_KEY, language);
  }
  function loadData() {
    const prodData = localStorage.getItem(PRODUCTS_KEY);
    products = prodData ? JSON.parse(prodData) : [];
    const custData = localStorage.getItem(CUSTOMERS_KEY);
    customers = custData ? JSON.parse(custData) : [];
    const invData = localStorage.getItem(INVOICES_KEY);
    invoices = invData ? JSON.parse(invData) : [];
    const langData = localStorage.getItem(LANG_KEY);
    language = langData === 'hi' ? 'hi' : 'en';
  }

  // Translate the static UI labels
  function translateUI() {
    document.getElementById('app-title').textContent = t('Vyapar Free');
    
    navButtons.forEach(btn => {
      const sec = btn.dataset.section;
      btn.textContent = t(capitalize(sec));
      btn.title = t(capitalize(sec));
    });

    document.getElementById('dashboard-title').textContent = t('Dashboard Summary');
    document.getElementById('products-title').textContent = t('Products Management');
    document.getElementById('customers-title').textContent = t('Customers Management');
    document.getElementById('invoices-title').textContent = t('Create Invoice');
    document.getElementById('reports-title').textContent = t('Sales Reports');

    // Translation for products form
    document.querySelector('label[for=product-code]').textContent = t('Product Code');
    document.querySelector('label[for=product-name]').textContent = t('Name');
    document.querySelector('label[for=product-price]').textContent = t('Price (₹)');
    document.querySelector('label[for=product-stock]').textContent = t('Stock Quantity');
    document.getElementById('product-submit').textContent = modeProductEdit ? t('Edit Product') : t('Add Product');
    document.getElementById('product-cancel').textContent = t('Cancel');

    // Products list heading
    document.querySelector('#products > hr + h3').textContent = t('Products List');

    // Customers form
    document.querySelector('label[for=customer-name]').textContent = t('Customer Name');
    document.querySelector('label[for=customer-phone]').textContent = t('Phone Number');
    document.querySelector('label[for=customer-gst]').textContent = t('GST Number');
    document.getElementById('customer-submit').textContent = modeCustomerEdit ? t('Edit Customer') : t('Add Customer');
    document.getElementById('customer-cancel').textContent = t('Cancel');

    document.querySelector('#customers > hr + h3').textContent = t('Customers List');

    // Invoices form
    document.querySelector('label[for=invoice-customer]').textContent = t('Select Customer');
    document.querySelector('label[for=invoice-customer]').setAttribute('aria-label', t('Select Customer'));

    document.querySelector('#invoice-form > div > label').textContent = t('Invoice Items');
    document.getElementById('add-invoice-item').textContent = t('Add Item');

    // Invoice totals
    document.querySelector('#invoices > div > p:nth-child(1)').childNodes[0].textContent = t('Sub Total') + ': ';
    document.querySelector('#invoices > div > p:nth-child(2)').childNodes[0].textContent = t('CGST (9%)') + ': ';
    document.querySelector('#invoices > div > p:nth-child(3)').childNodes[0].textContent = t('SGST (9%)') + ': ';
    document.querySelector('#invoices > div > p:nth-child(4) strong').childNodes[0].textContent = t('Total') + ': ';

    document.getElementById('invoice-submit').textContent = t('Save Invoice');
    document.querySelector('#invoices > hr + h3').textContent = t('Invoices List');

    // Sales Reports heading
    document.getElementById('reports-title').textContent = t('Sales Reports');

    // Language toggle button
    langToggleBtn.textContent = language === 'en' ? t('Hindi') : t('English');
    langToggleBtn.title = t('Toggle Language');
  }

  // Capitalize first letter utility
  function capitalize(s) {
    if (typeof s !== 'string') return '';
    return s.charAt(0).toUpperCase() + s.slice(1);
  }

  // Navigation handling
  navButtons.forEach(btn => {
    btn.addEventListener('click', () => {
      navButtons.forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      const sec = btn.dataset.section;
      sections.forEach(s => s.classList.remove('active'));
      const activeSection = document.getElementById(sec);
      if (activeSection) {
        activeSection.classList.add('active');
        activeSection.focus();
      }
      if (sec === 'invoices') {
        populateInvoiceCustomerOptions();
        renderInvoiceItemsTable();
        renderInvoicesTable();
      }
      if (sec === 'reports') {
        renderReportTable();
      }
      if (sec === 'products') {
        renderProductsTable();
      }
      if (sec === 'customers') {
        renderCustomersTable();
      }
      if (sec === 'dashboard') {
        renderDashboard();
      }
    });
  });

  // Language toggle button
  langToggleBtn.addEventListener('click', () => {
    language = language === 'en' ? 'hi' : 'en';
    saveData();
    translateUI();
    // Re-render UI section data as needed
    renderDashboard();
    renderProductsTable();
    renderCustomersTable();
    populateInvoiceCustomerOptions();
    renderInvoiceItemsTable();
    renderInvoicesTable();
    renderReportTable();
  });

  // Product form & list
  const productForm = document.getElementById('product-form');
  const productCodeInput = document.getElementById('product-code');
  const productNameInput = document.getElementById('product-name');
  const productPriceInput = document.getElementById('product-price');
  const productStockInput = document.getElementById('product-stock');
  const productsTableBody = document.querySelector('#products-table tbody');
  const productCancelBtn = document.getElementById('product-cancel');

  let modeProductEdit = false;
  let editingProductCode = null;

  function renderProductsTable() {
    productsTableBody.innerHTML = '';
    if (products.length === 0) {
      productsTableBody.innerHTML = `<tr><td colspan="5" style="text-align:center; color:#777;">${t('No data available')}</td></tr>`;
      return;
    }
    products.forEach(prod => {
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${escapeHtml(prod.code)}</td>
        <td>${escapeHtml(prod.name)}</td>
        <td class="numeric">₹${prod.price.toFixed(2)}</td>
        <td class="numeric">${prod.stock}</td>
        <td>
          <button aria-label="${t('Edit')} ${escapeHtml(prod.name)}" class="btn-inline secondary" data-code="${escapeHtml(prod.code)}" data-action="edit">${t('Edit')}</button>
          <button aria-label="${t('Delete')} ${escapeHtml(prod.name)}" class="btn-inline danger" data-code="${escapeHtml(prod.code)}" data-action="delete">${t('Delete')}</button>
        </td>
      `;
      productsTableBody.appendChild(tr);
    });
  }
  function resetProductForm() {
    modeProductEdit = false;
    editingProductCode = null;
    productCodeInput.disabled = false;
    productForm.reset();
    productCancelBtn.style.display = 'none';
    productForm.querySelector('button[type=submit]').textContent = t('Add Product');
  }
  productForm.addEventListener('submit', e => {
    e.preventDefault();
    let code = productCodeInput.value.trim();
    let name = productNameInput.value.trim();
    let price = parseFloat(productPriceInput.value);
    let stock = parseInt(productStockInput.value);
    if (!code || !name || isNaN(price) || price < 0 || isNaN(stock) || stock < 0) {
      showToast(t('Please fill all fields correctly'));
      return;
    }
    code = code.toUpperCase();
    if (modeProductEdit) {
      // Update product
      const index = products.findIndex(p => p.code === editingProductCode);
      if (index !== -1) {
        products[index].name = name;
        products[index].price = price;
        products[index].stock = stock;
        showToast(t('Product updated'));
      }
    } else {
      // Add new product
      if (products.some(p => p.code === code)) {
        showToast('Product code already exists');
        return;
      }
      products.push({code, name, price, stock});
      showToast(t('Product added'));
    }
    saveData();
    renderProductsTable();
    resetProductForm();
  });
  productsTableBody.addEventListener('click', e => {
    if (e.target.tagName === 'BUTTON') {
      const code = e.target.dataset.code;
      const action = e.target.dataset.action;
      if (action === 'edit') {
        const prod = products.find(p => p.code === code);
        if (prod) {
          modeProductEdit = true;
          editingProductCode = code;
          productCodeInput.value = prod.code;
          productCodeInput.disabled = true;
          productNameInput.value = prod.name;
          productPriceInput.value = prod.price.toFixed(2);
          productStockInput.value = prod.stock;
          productCancelBtn.style.display = 'inline-block';
          productForm.querySelector('button[type=submit]').textContent = t('Edit Product');
          navButtons.forEach(b => b.classList.remove('active'));
          document.querySelector('nav button[data-section="products"]').classList.add('active');
          sections.forEach(s => s.classList.remove('active'));
          document.getElementById('products').classList.add('active');
          productNameInput.focus();
        }
      } else if (action === 'delete') {
        if (confirm(t('Delete') + ` "${code}"?`)) {
          products = products.filter(p => p.code !== code);
          showToast(t('Product deleted'));
          saveData();
          renderProductsTable();
        }
      }
    }
  });
  productCancelBtn.addEventListener('click', resetProductForm);

  // Customers form & list
  const customerForm = document.getElementById('customer-form');
  const customerNameInput = document.getElementById('customer-name');
  const customerPhoneInput = document.getElementById('customer-phone');
  const customerGSTInput = document.getElementById('customer-gst');
  const customersTableBody = document.querySelector('#customers-table tbody');
  const customerCancelBtn = document.getElementById('customer-cancel');

  let modeCustomerEdit = false;
  let editingCustomerIndex = null;

  function renderCustomersTable() {
    customersTableBody.innerHTML = '';
    if (customers.length === 0) {
      customersTableBody.innerHTML = `<tr><td colspan="4" style="text-align:center; color:#777;">${t('No data available')}</td></tr>`;
      return;
    }
    customers.forEach((cust, i) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${escapeHtml(cust.name)}</td>
        <td>${escapeHtml(cust.phone)}</td>
        <td>${escapeHtml(cust.gst)}</td>
        <td>
          <button aria-label="${t('Edit')} ${escapeHtml(cust.name)}" class="btn-inline secondary" data-index="${i}" data-action="edit">${t('Edit')}</button>
          <button aria-label="${t('Delete')} ${escapeHtml(cust.name)}" class="btn-inline danger" data-index="${i}" data-action="delete">${t('Delete')}</button>
        </td>
      `;
      customersTableBody.appendChild(tr);
    });
  }
  function resetCustomerForm() {
    modeCustomerEdit = false;
    editingCustomerIndex = null;
    customerForm.reset();
    customerCancelBtn.style.display = 'none';
    customerForm.querySelector('button[type=submit]').textContent = t('Add Customer');
  }
  customerForm.addEventListener('submit', e => {
    e.preventDefault();
    let name = customerNameInput.value.trim();
    let phone = customerPhoneInput.value.trim();
    let gst = customerGSTInput.value.trim().toUpperCase();
    if (!name) {
      showToast(t('Please fill all fields correctly'));
      return;
    }
    if (modeCustomerEdit) {
      customers[editingCustomerIndex] = {name, phone, gst};
      showToast(t('Customer updated'));
    } else {
      customers.push({name, phone, gst});
      showToast(t('Customer added'));
    }
    saveData();
    renderCustomersTable();
    resetCustomerForm();
  });
  customersTableBody.addEventListener('click', e => {
    if (e.target.tagName === 'BUTTON') {
      const index = parseInt(e.target.dataset.index, 10);
      const action = e.target.dataset.action;
      if (action === 'edit') {
        const cust = customers[index];
        if (cust) {
          modeCustomerEdit = true;
          editingCustomerIndex = index;
          customerNameInput.value = cust.name;
          customerPhoneInput.value = cust.phone;
          customerGSTInput.value = cust.gst;
          customerCancelBtn.style.display = 'inline-block';
          customerForm.querySelector('button[type=submit]').textContent = t('Edit Customer');
          navButtons.forEach(b => b.classList.remove('active'));
          document.querySelector('nav button[data-section="customers"]').classList.add('active');
          sections.forEach(s => s.classList.remove('active'));
          document.getElementById('customers').classList.add('active');
          customerNameInput.focus();
        }
      } else if (action === 'delete') {
        if (confirm(t('Delete') + ` "${customers[index].name}"?`)) {
          customers.splice(index, 1);
          showToast(t('Customer deleted'));
          saveData();
          renderCustomersTable();
        }
      }
    }
  });
  customerCancelBtn.addEventListener('click', resetCustomerForm);

  // Invoice creation form
  const invoiceForm = document.getElementById('invoice-form');
  const invoiceCustomerSelect = document.getElementById('invoice-customer');
  const invoiceItemsTableBody = document.querySelector('#invoice-items-table tbody');
  const invoiceSubtotalEl = document.getElementById('invoice-subtotal');
  const invoiceCGSTEl = document.getElementById('invoice-cgst');
  const invoiceSGSTEl = document.getElementById('invoice-sgst');
  const invoiceTotalEl = document.getElementById('invoice-total');
  const addInvoiceItemBtn = document.getElementById('add-invoice-item');
  const invoicesTableBody = document.querySelector('#invoices-table tbody');

  let invoiceItems = [];

  // Escape HTML utility to prevent XSS
  function escapeHtml(text) {
    if (!text) return '';
    return text.replace(/[&<>"']/g, function(m) {
      switch (m) {
        case '&': return '&amp;';
        case '<': return '&lt;';
        case '>': return '&gt;';
        case '"': return '&quot;';
        case "'": return '&#39;';
        default: return m;
      }
    });
  }

  // Populate customer select options for invoice
  function populateInvoiceCustomerOptions() {
    invoiceCustomerSelect.innerHTML = `<option value="" disabled selected>${t('Select Customer')}</option>`;
    customers.forEach((c, i) => {
      const option = document.createElement('option');
      option.value = i;
      option.textContent = c.name;
      invoiceCustomerSelect.appendChild(option);
    });
  }
  // Populate product options for invoice item row
  function createProductOptions(selectedCode) {
    const select = document.createElement('select');
    select.name = 'product-select';
    select.required = true;
    select.setAttribute('aria-label', t('Select product'));
    const defaultOption = document.createElement('option');
    defaultOption.value = '';
    defaultOption.textContent = `-- ${t('Select product')} --`;
    defaultOption.disabled = true;
    defaultOption.selected = true;
    select.appendChild(defaultOption);
    products.forEach(p => {
      const option = document.createElement('option');
      option.value = p.code;
      option.textContent = `${p.code} - ${p.name}`;
      if (p.code === selectedCode) {
        option.selected = true;
      }
      select.appendChild(option);
    });
    return select;
  }
  // Render invoice items rows
  function renderInvoiceItemsTable() {
    invoiceItemsTableBody.innerHTML = '';
    if (invoiceItems.length === 0) {
      // add an empty item row by default
      addInvoiceItem();
    }
    invoiceItems.forEach((item, idx) => {
      const tr = document.createElement('tr');
      const productSelect = createProductOptions(item.productCode);
      productSelect.addEventListener('change', e => {
        const code = e.target.value;
        const prod = products.find(p => p.code === code);
        if (prod) {
          invoiceItems[idx].productCode = code;
          invoiceItems[idx].price = prod.price;
          invoiceItems[idx].quantity = invoiceItems[idx].quantity || 1;
          renderInvoiceItemsTable();
          calculateInvoiceTotals();
        }
      });
      const priceInput = document.createElement('input');
      priceInput.type = 'number';
      priceInput.min = '0';
      priceInput.step = '0.01';
      priceInput.value = item.price !== undefined ? item.price.toFixed(2) : '';
      priceInput.style.width = '70px';
      priceInput.setAttribute('aria-label', t('Price (₹)'));
      priceInput.addEventListener('input', e => {
        const val = parseFloat(e.target.value);
        if (!isNaN(val) && val >= 0) {
          invoiceItems[idx].price = val;
          calculateInvoiceTotals();
        }
      });
      const qtyInput = document.createElement('input');
      qtyInput.type = 'number';
      qtyInput.min = '1';
      qtyInput.step = '1';
      qtyInput.value = item.quantity || 1;
      qtyInput.style.width = '50px';
      qtyInput.setAttribute('aria-label', t('Quantity'));
      qtyInput.addEventListener('input', e => {
        const val = parseInt(e.target.value, 10);
        if (!isNaN(val) && val > 0) {
          invoiceItems[idx].quantity = val;
          calculateInvoiceTotals();
        }
      });
      const totalTd = document.createElement('td');
      totalTd.classList.add('numeric');
      totalTd.textContent = '₹' + ((item.price || 0) * (item.quantity || 0)).toFixed(2);

      const actionTd = document.createElement('td');
      const removeBtn = document.createElement('button');
      removeBtn.type = 'button';
      removeBtn.className = 'danger btn-inline';
      removeBtn.textContent = t('Delete');
      removeBtn.title = t('Delete');
      removeBtn.addEventListener('click', () => {
        invoiceItems.splice(idx, 1);
        if (invoiceItems.length === 0) addInvoiceItem();
        renderInvoiceItemsTable();
        calculateInvoiceTotals();
        showToast(t('Item removed'));
      });
      actionTd.appendChild(removeBtn);

      const productTd = document.createElement('td');
      productTd.appendChild(productSelect);
      const priceTd = document.createElement('td');
      priceTd.appendChild(priceInput);
      const qtyTd = document.createElement('td');
      qtyTd.appendChild(qtyInput);

      tr.appendChild(productTd);
      tr.appendChild(priceTd);
      tr.appendChild(qtyTd);
      tr.appendChild(totalTd);
      tr.appendChild(actionTd);

      invoiceItemsTableBody.appendChild(tr);
    });
  }
  // Add new empty invoice item row
  function addInvoiceItem() {
    invoiceItems.push({productCode: '', price: 0, quantity: 1});
  }
  addInvoiceItemBtn.addEventListener('click', () => {
    if (products.length === 0) {
      showToast(t('Please add products before creating invoice'));
      return;
    }
    addInvoiceItem();
    renderInvoiceItemsTable();
  });
  // Calculate totals for invoice
  function calculateInvoiceTotals() {
    let subtotal = 0;
    invoiceItems.forEach(item => {
      subtotal += (item.price || 0) * (item.quantity || 0);
    });
    const cgst = subtotal * CGST_RATE;
    const sgst = subtotal * SGST_RATE;
    const total = subtotal + cgst + sgst;
    invoiceSubtotalEl.textContent = subtotal.toFixed(2);
    invoiceCGSTEl.textContent = cgst.toFixed(2);
    invoiceSGSTEl.textContent = sgst.toFixed(2);
    invoiceTotalEl.textContent = total.toFixed(2);
  }
  // Invoice form submit
  invoiceForm.addEventListener('submit', e => {
    e.preventDefault();
    const custIndex = parseInt(invoiceCustomerSelect.value, 10);
    if (isNaN(custIndex) || custIndex < 0 || custIndex >= customers.length) {
      showToast(t('Please select a customer'));
      return;
    }
    if (invoiceItems.length === 0 || invoiceItems.some(item => !item.productCode || !item.price || !item.quantity)) {
      showToast(t('Please fill all fields correctly'));
      return;
    }
    // Verify products still exist and price is valid
    for (const item of invoiceItems) {
      const prod = products.find(p => p.code === item.productCode);
      if (!prod) {
        showToast(t('Please add products before creating invoice'));
        return;
      }
      if (item.price < 0 || item.quantity <= 0) {
        showToast(t('Please fill all fields correctly'));
        return;
      }
    }

    // Prepare invoice data object
    const subtotal = invoiceItems.reduce((sum, item) => sum + item.price * item.quantity, 0);
    const cgst = subtotal * CGST_RATE;
    const sgst = subtotal * SGST_RATE;
    const total = subtotal + cgst + sgst;
    const invoice = {
      id: Date.now(),
      customerIndex: custIndex,
      customerName: customers[custIndex].name,
      date: new Date().toISOString().slice(0,10),
      items: JSON.parse(JSON.stringify(invoiceItems)),
      subtotal,
      cgst,
      sgst,
      total
    };
    invoices.push(invoice);
    showToast(t('Invoice saved'));
    saveData();
    renderInvoicesTable();
    resetInvoiceForm();
    renderDashboard();
    renderReportTable();
  });
  // Reset invoice form
  function resetInvoiceForm() {
    invoiceCustomerSelect.value = '';
    invoiceItems = [];
    addInvoiceItem();
    renderInvoiceItemsTable();
    calculateInvoiceTotals();
  }
  // Render invoices list table
  function renderInvoicesTable() {
    invoicesTableBody.innerHTML = '';
    if (invoices.length === 0) {
      invoicesTableBody.innerHTML = `<tr><td colspan="5" style="text-align:center; color:#777;">${t('No data available')}</td></tr>`;
      return;
    }
    invoices.forEach((inv, i) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${i + 1}</td>
        <td>${escapeHtml(inv.customerName)}</td>
        <td>${escapeHtml(inv.date)}</td>
        <td class="numeric">₹${inv.total.toFixed(2)}</td>
        <td>
          <button class="btn-inline secondary" data-id="${inv.id}" data-action="view">${t('View')}</button>
          <button class="btn-inline danger" data-id="${inv.id}" data-action="delete">${t('Delete')}</button>
        </td>
      `;
      invoicesTableBody.appendChild(tr);
    });
  }
  // Handle invoice list actions
  invoicesTableBody.addEventListener('click', e => {
    if (e.target.tagName === 'BUTTON') {
      const id = parseInt(e.target.dataset.id, 10);
      const action = e.target.dataset.action;
      const idx = invoices.findIndex(inv => inv.id === id);
      if (idx === -1) return;
      if (action === 'delete') {
        if (confirm(t('Delete') + ` ${t('Invoice #')} ${id}?`)) {
          invoices.splice(idx, 1);
          showToast('Invoice deleted');
          saveData();
          renderInvoicesTable();
          renderDashboard();
          renderReportTable();
        }
      } else if (action === 'view') {
        alert(generateInvoiceText(invoices[idx]));
      }
    }
  });
  // Generate plain text invoice details for alert view
  function generateInvoiceText(inv) {
    let text = `${t('Invoice #')}: ${inv.id}\n`;
    text += `${t('Date')}: ${inv.date}\n`;
    text += `${t('Customer')}: ${inv.customerName}\n`;
    text += `\n${t('Invoice Items')}\n`;
    text += `--------------------------------\n`;
    inv.items.forEach(item => {
      text += `${item.productCode} x${item.quantity} @ ₹${item.price.toFixed(2)} = ₹${(item.price * item.quantity).toFixed(2)}\n`;
    });
    text += `--------------------------------\n`;
    text += `${t('Sub Total')}: ₹${inv.subtotal.toFixed(2)}\n`;
    text += `${t('CGST (9%)')}: ₹${inv.cgst.toFixed(2)}\n`;
    text += `${t('SGST (9%)')}: ₹${inv.sgst.toFixed(2)}\n`;
    text += `${t('Total')}: ₹${inv.total.toFixed(2)}\n`;
    return text;
  }

  // Dashboard rendering
  const totalProductsEl = document.getElementById('total-products');
  const totalCustomersEl = document.getElementById('total-customers');
  const totalInvoicesEl = document.getElementById('total-invoices');
  const totalSalesEl = document.getElementById('total-sales');
  function renderDashboard() {
    totalProductsEl.textContent = products.length;
    totalCustomersEl.textContent = customers.length;
    totalInvoicesEl.textContent = invoices.length;
    const totalSales = invoices.reduce((sum, inv) => sum + inv.total, 0);
    totalSalesEl.textContent = `₹${totalSales.toFixed(2)}`;
  }

  // Reports rendering
  const reportsTableBody = document.querySelector('#reports-table tbody');
  function renderReportTable() {
    reportsTableBody.innerHTML = '';
    if (invoices.length === 0) {
      reportsTableBody.innerHTML = `<tr><td colspan="4" style="text-align:center; color:#777;">${t('No data available')}</td></tr>`;
      return;
    }
    invoices.forEach((inv, i) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${i + 1}</td>
        <td>${escapeHtml(inv.date)}</td>
        <td>${escapeHtml(inv.customerName)}</td>
        <td class="numeric">₹${inv.total.toFixed(2)}</td>
      `;
      reportsTableBody.appendChild(tr);
    });
  }

  // Initialize app
  function init() {
    loadData();
    translateUI();
    renderDashboard();
    renderProductsTable();
    renderCustomersTable();
    populateInvoiceCustomerOptions();
    resetInvoiceForm();
    renderInvoicesTable();
    renderReportTable();
  }

  init();
})();
</script>

</body>
</html>

```

