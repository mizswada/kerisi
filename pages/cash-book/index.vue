<template>
  <div>
    <LayoutsBreadcrumb />

    <!-- File Upload Form Card -->
    <rs-card v-if="isNormalUpload">
      <template #header>
        <h2 class="text-xl font-semibold">Import Bank Statement</h2>
      </template>
      <template #body>
        <FormKit
          type="form"
          @submit="handleFileUpload"
          submit-label="Upload Statement"
          :actions="false"
          id="uploadForm"
        >
          <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
            <!-- File Upload Type -->
            <FormKit
              type="select"
              name="fileType"
              label="File Upload Type"
              placeholder="Select file type"
              :options="fileTypeOptions"
              validation="required"
              help="Choose the type of file you want to upload"
              v-model="formData.fileType"
            />

            <!-- Account Code -->
            <FormKit
              type="select"
              name="accountCode"
              label="Account Code"
              placeholder="Select account code"
              :options="accountCodeOptions"
              validation="required"
              help="Select the bank account code"
              v-model="formData.accountCode"
              @input="updateBankAccountNumber"
            />

            <!-- Account Bank No -->
            <FormKit
              type="text"
              name="accountBankNo"
              label="Account Bank No"
              placeholder="Select account code first"
              validation="required"
              help="Bank account number (auto-filled based on selected account code)"
              v-model="formData.accountBankNo"
              readonly
              disabled
            />

            <!-- File Upload -->
            <FormKit
              type="file"
              name="bankStatementFile"
              label="File Name"
              accept=".csv,.txt,.xlsx,.pdf"
              validation="required"
              help="Upload your bank statement file (CSV, TXT, XLSX, or PDF format)"
              @change="handleFileChange"
              v-model="formData.bankStatementFile"
            />
          </div>

          <div class="flex justify-end mt-6 gap-3">
            <rs-button type="button" variant="secondary" @click="resetForm">
              Reset
            </rs-button>
            <rs-button type="button" variant="primary" @click="submitForm" :disabled="!isFormValid">
              <Icon name="ic:outline-file-upload" class="mr-2" size="1rem" />
              Upload Statement
            </rs-button>
            <rs-button variant="info" @click="handleAIUpload" class="ai-upload-btn">
              <Icon name="ic:outline-auto-awesome" class="mr-2" size="1rem" />
              Upload With AI
            </rs-button>
          </div>
        </FormKit>
      </template>
    </rs-card>

    <rs-card v-if="isAIUpload">
      <template #header>
        <h2 class="text-xl font-semibold">Import Bank Statement With AI</h2>
      </template>
      <template #body>
        <FormKit
          type="form"
          @submit="handleFileUpload"
          submit-label="Upload Statement"
          :actions="false"
          id="uploadForm"
        >
          <div class="grid grid-cols-1 md:grid-cols-1 gap-6">
               <!-- File Upload -->
            <FormKit
              type="file"
              name="bankStatementAIFile"
              label="File Name"
              accept=".csv,.txt,.xlsx,.pdf"
              validation="required"
              help="Upload your bank statement file (CSV, TXT, XLSX, or PDF format)"
              @change="handleFileChange"
              v-model="formData.bankStatementAIFile"
            />
          </div>

          <div class="flex justify-end mt-6 gap-3">
            <rs-button type="button" variant="secondary" @click="resetForm">
              Reset
            </rs-button>
            <rs-button type="button" variant="info" @click="switchToNormalUpload">
              <Icon name="ic:outline-file-upload" class="mr-2" size="1rem" />
              Normal Upload
            </rs-button>
            <rs-button type="button" variant="primary" @click="submitForm" :disabled="!formData.bankStatementAIFile">
              <Icon name="ic:outline-file-upload" class="mr-2" size="1rem" />
              Upload Statement AI
            </rs-button>
          </div>
        </FormKit>
      </template>
    </rs-card>

    <!-- Placeholder when no document uploaded -->
    <rs-card v-if="!isDocumentUploaded">
      <template #header>
        <h2 class="text-xl font-semibold">Bank Statement Data</h2>
      </template>
      <template #body>
        <div class="text-center py-12">
          <Icon name="ic:outline-cloud-upload" class="text-gray-300 mb-4" size="64px" />
          <h3 class="text-lg font-medium text-gray-600 mb-2">No Document Uploaded</h3>
          <p class="text-gray-500">
            Please upload a bank statement document using the form above to view the data table.
          </p>
        </div>
      </template>
    </rs-card>

    <!-- File Details Tab Card -->
    <rs-card v-if="isDocumentUploaded && isAIUpload">
      <template #header>
        <h2 class="text-xl font-semibold">Uploaded File Details</h2>
      </template>
      <template #body>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="bg-gray-50 p-4 rounded-lg">
            <div class="flex items-center mb-2">
              <Icon name="ic:outline-description" class="mr-2 text-blue-600" size="1.25rem" />
              <h3 class="font-semibold text-gray-800">File Upload Type</h3>
            </div>
            <p class="text-gray-600">{{ uploadedFileDetails.fileType }}</p>
          </div>
          
          <div class="bg-gray-50 p-4 rounded-lg">
            <div class="flex items-center mb-2">
              <Icon name="ic:outline-account-balance" class="mr-2 text-green-600" size="1.25rem" />
              <h3 class="font-semibold text-gray-800">Account Code</h3>
            </div>
            <p class="text-gray-600">{{ uploadedFileDetails.accountCode }}</p>
          </div>
          
          <div class="bg-gray-50 p-4 rounded-lg">
            <div class="flex items-center mb-2">
              <Icon name="ic:outline-credit-card" class="mr-2 text-purple-600" size="1.25rem" />
              <h3 class="font-semibold text-gray-800">Account Bank No</h3>
            </div>
            <p class="text-gray-600">{{ uploadedFileDetails.accountBankNo }}</p>
          </div>
        </div>
        
        <div class="mt-4 p-4 bg-blue-50 rounded-lg">
          <div class="flex items-center">
            <Icon name="ic:outline-info" class="mr-2 text-blue-600" size="1.25rem" />
            <div>
              <h4 class="font-semibold text-blue-800">AI Processing Information</h4>
              <p class="text-blue-700 text-sm mt-1">
                This document was processed using AI technology for enhanced data extraction and categorization.
              </p>
            </div>
          </div>
        </div>
      </template>
    </rs-card>

    <!-- Bank Statement Data Table Card -->
    <rs-card v-if="isDocumentUploaded">
      <template #header>
        <div class="flex justify-between items-center">
          <h2 class="text-xl font-semibold">Bank Statement Data</h2>
          <div class="text-sm text-gray-600">
            <Icon name="ic:outline-description" class="mr-1" size="1rem" />
            {{ uploadedFileName }}
          </div>
        </div>
      </template>
      <template #body>
        <rs-table
          :field="tableHeaders"
          :data="bankStatementData"
          :options="{
            variant: 'default',
            striped: true,
            borderless: false,
            hover: true,
            fixed: false,
          }"
          :optionsAdvanced="{
            sortable: true,
            filterable: true,
            responsive: true,
            outsideBorder: true,
          }"
          :pageSize="10"
          advanced
        >
          <!-- Custom formatting for currency columns -->
          <template #debitAmountRM="{ text }">
            <span v-if="text && text !== '-'" class="text-red-600 font-medium">
              {{ formatCurrency(text) }}
            </span>
            <span v-else class="text-gray-400">-</span>
          </template>
          
          <template #creditAmountRM="{ text }">
            <span v-if="text && text !== '-'" class="text-green-600 font-medium">
              {{ formatCurrency(text) }}
            </span>
            <span v-else class="text-gray-400">-</span>
          </template>
          
          <template #balanceRM="{ text }">
            <span class="font-medium" :class="parseFloat(text) >= 0 ? 'text-green-600' : 'text-red-600'">
              {{ formatCurrency(text) }}
            </span>
          </template>
          
          <!-- Transaction Type Badge -->
          <template #transactionType="{ text }">
            <rs-badge 
              :variant="text === 'Credit' ? 'success' : text === 'Debit' ? 'danger' : 'secondary'"
              size="sm"
            >
              {{ text }}
            </rs-badge>
          </template>
          
          <!-- Bank Statement Date formatting -->
          <template #bankStatementDate="{ text }">
            <span class="text-sm">{{ formatDate(text) }}</span>
          </template>
        </rs-table>
      </template>
    </rs-card>
  </div>
</template>

<script setup>
definePageMeta({
  title: "Cash Book - Bank Statement",
  middleware: 'auth'
});

// Get $swal from nuxt app
const { $swal } = useNuxtApp();
const isAIUpload = ref(false);
const isNormalUpload = ref(true);
// Ensure $swal is available
if (!$swal) {
  console.error('SweetAlert2 is not available');
}

// Form data for file upload
const formData = ref({
  fileType: '',
  accountCode: '',
  accountBankNo: '',
  bankStatementFile: null,
  bankStatementAIFile: null
});

// Upload state management
const isDocumentUploaded = ref(false);
const uploadedFileName = ref('');
const uploadedFileDetails = ref({
  fileType: '',
  accountCode: '',
  accountBankNo: '',
  fileName: ''
});

// Dropdown options for file upload form
const fileTypeOptions = [
  { label: 'CSV (OTHERS)', value: 'csv_others' },
  { label: 'CSV (Standard)', value: 'csv_standard' },
  { label: 'TXT (Delimited)', value: 'txt_delimited' },
  { label: 'Excel (XLSX)', value: 'xlsx' },
  { label: 'PDF Document', value: 'pdf' }
];

const accountCodeOptions = [
  { label: 'A0111101 - BIMB - OPERASI', value: 'A0111101' },
  { label: 'A0111102 - BIMB - SAVINGS', value: 'A0111102' },
  { label: 'A0111103 - MAYBANK - CURRENT', value: 'A0111103' },
  { label: 'A0111104 - CIMB - BUSINESS', value: 'A0111104' },
  { label: 'A0111105 - PUBLIC BANK - OPERASI', value: 'A0111105' },
  { label: 'A0111106 - RHB - CURRENT', value: 'A0111106' }
];

// Mapping between Account Code and Bank Account Numbers
const accountBankMapping = {
  'A0111101': '020930100000010', // BIMB - OPERASI
  'A0111102': '020930100000025', // BIMB - SAVINGS
  'A0111103': '512345678901234', // MAYBANK - CURRENT
  'A0111104': '810123456789012', // CIMB - BUSINESS
  'A0111105': '315987654321098', // PUBLIC BANK - OPERASI
  'A0111106': '214567890123456'  // RHB - CURRENT
};

// Form handling functions
const handleFileUpload = async (formValues) => {
  try {
    console.log('Form submitted with values:', formValues);
    
    // Here you would typically send the data to your API
    // Example API call:
    // const response = await $fetch('/api/bank-statement/upload', {
    //   method: 'POST',
    //   body: formData
    // });
    
    // Simulate processing time
    await new Promise(resolve => setTimeout(resolve, 1500));
    
    // Store uploaded file information based on upload mode
    if (isAIUpload.value) {
      uploadedFileName.value = formValues.bankStatementAIFile?.name || 'AI Document';
      
      // Store AI upload details for the tab
      uploadedFileDetails.value = {
        fileType: getFileTypeFromName(formValues.bankStatementAIFile?.name),
        accountCode: 'A0111101 - BIMB - OPERASI', // Default for AI upload
        accountBankNo: '020930100000010', // Default for AI upload
        fileName: formValues.bankStatementAIFile?.name || 'AI Document'
      };
    } else {
      uploadedFileName.value = formValues.bankStatementFile?.name || 'Document';
      
      // Store normal upload details
      uploadedFileDetails.value = {
        fileType: formValues.fileType || '',
        accountCode: formValues.accountCode || '',
        accountBankNo: formValues.accountBankNo || '',
        fileName: formValues.bankStatementFile?.name || 'Document'
      };
    }
    isDocumentUploaded.value = true;
    
    // Show success message
    await $swal.fire({
      title: 'Success!',
      text: `Document has been processed successfully.`,
      icon: 'success',
      confirmButtonText: 'OK'
    });
    
    // Optionally refresh the table data or redirect
    // await refreshTableData();
    
  } catch (error) {
    console.error('Upload error:', error);
    await $swal.fire({
      title: 'Error!',
      text: 'Failed to upload document. Please try again.',
      icon: 'error',
      confirmButtonText: 'OK'
    });
  }
};

const handleFileChange = (event) => {
  const file = event.target?.files?.[0] || event;
  if (file) {
    // Determine which file field to update based on current mode
    if (isAIUpload.value) {
      formData.value.bankStatementAIFile = file;
      console.log('AI File selected:', file.name);
    } else {
      formData.value.bankStatementFile = file;
      console.log('Normal File selected:', file.name);
    }
    
    // Validate file type
    const allowedTypes = ['.csv', '.txt', '.xlsx', '.pdf'];
    const fileExtension = '.' + file.name.split('.').pop().toLowerCase();
    
    if (!allowedTypes.includes(fileExtension)) {
      $swal.fire({
        title: 'Invalid File Type!',
        text: 'Please select a CSV, TXT, XLSX, or PDF file.',
        icon: 'error',
        confirmButtonText: 'OK'
      });
      
      // Clear the appropriate file field
      if (isAIUpload.value) {
        formData.value.bankStatementAIFile = null;
      } else {
        formData.value.bankStatementFile = null;
      }
      return;
    }
  }
};

const updateBankAccountNumber = (accountCode) => {
  if (accountCode && accountBankMapping[accountCode]) {
    formData.value.accountBankNo = accountBankMapping[accountCode];
  } else {
    formData.value.accountBankNo = '';
  }
};

const submitForm = async () => {
  try {
    // Validate form data manually since we're using custom submit
    if (!isFormValid.value) {
      await $swal.fire({
        title: 'Validation Error!',
        text: 'Please fill in all required fields before submitting.',
        icon: 'warning',
        confirmButtonText: 'OK'
      });
      return;
    }

    // Call the handleFileUpload function with form data
    await handleFileUpload(formData.value);
  } catch (error) {
    console.error('Form submission error:', error);
  }
};

const resetForm = () => {
  formData.value = {
    fileType: '',
    accountCode: '',
    accountBankNo: '',
    bankStatementFile: null,
    bankStatementAIFile: null
  };
  
  // Reset upload state
  isDocumentUploaded.value = false;
  uploadedFileName.value = '';
  uploadedFileDetails.value = {
    fileType: '',
    accountCode: '',
    accountBankNo: '',
    fileName: ''
  };
};

// Computed property to check if form is valid
const isFormValid = computed(() => {
  if (isAIUpload.value) {
    // For AI upload, only file is required
    return !!formData.value.bankStatementAIFile;
  } else {
    // For normal upload, all fields are required
    return !!(
      formData.value.fileType &&
      formData.value.accountCode &&
      formData.value.accountBankNo &&
      formData.value.bankStatementFile
    );
  }
});

// AI Upload handler - Toggle between normal and AI upload modes
const handleAIUpload = () => {
  isNormalUpload.value = false;
  isAIUpload.value = true;
  
  // Reset form when switching modes
  resetForm();
};

// Switch back to normal upload mode
const switchToNormalUpload = () => {
  isNormalUpload.value = true;
  isAIUpload.value = false;
  
  // Reset form when switching modes
  resetForm();
};

// Watch for changes in account code to update bank account number
watch(() => formData.value.accountCode, (newAccountCode) => {
  updateBankAccountNumber(newAccountCode);
});

// Table headers matching the user's requirements
const tableHeaders = [
  "No",
  "Bank Sequence ID", 
  "Bank Statement Date",
  "Bank Transcode",
  "Transaction Description",
  "Transaction Type",
  "Debit Amount (RM)",
  "Credit Amount (RM)", 
  "Balance (RM)",
  "FPX No",
  "Payment Details",
  "Bank Remark"
];

// Sample bank statement data
const bankStatementData = ref([
  {
    no: 1,
    bankSequenceId: "BSQ001234567",
    bankStatementDate: "2024-01-15",
    bankTranscode: "TXN2024011501",
    transactionDescription: "Online Transfer - Salary Payment",
    transactionType: "Credit",
    debitAmountRM: "-",
    creditAmountRM: "5500.00",
    balanceRM: "12750.50", 
    fpxNo: "FPX240115001",
    paymentDetails: "Monthly Salary - Company ABC Sdn Bhd",
    bankRemark: "Processed successfully"
  },
  {
    no: 2,
    bankSequenceId: "BSQ001234568",
    bankStatementDate: "2024-01-16",
    bankTranscode: "TXN2024011602",
    transactionDescription: "ATM Withdrawal",
    transactionType: "Debit",
    debitAmountRM: "500.00",
    creditAmountRM: "-",
    balanceRM: "12250.50",
    fpxNo: "-",
    paymentDetails: "Cash withdrawal at ATM Pavilion KL",
    bankRemark: "Transaction completed"
  },
  {
    no: 3,
    bankSequenceId: "BSQ001234569", 
    bankStatementDate: "2024-01-17",
    bankTranscode: "TXN2024011703",
    transactionDescription: "Online Purchase - E-commerce",
    transactionType: "Debit",
    debitAmountRM: "125.80",
    creditAmountRM: "-",
    balanceRM: "12124.70",
    fpxNo: "FPX240117002",
    paymentDetails: "Shopee - Electronics purchase",
    bankRemark: "Payment authorized"
  },
  {
    no: 4,
    bankSequenceId: "BSQ001234570",
    bankStatementDate: "2024-01-18",
    bankTranscode: "TXN2024011804",
    transactionDescription: "Direct Debit - Utility Bill",
    transactionType: "Debit", 
    debitAmountRM: "240.50",
    creditAmountRM: "-",
    balanceRM: "11884.20",
    fpxNo: "-",
    paymentDetails: "TNB Electricity Bill - Auto Debit",
    bankRemark: "Scheduled payment"
  },
  {
    no: 5,
    bankSequenceId: "BSQ001234571",
    bankStatementDate: "2024-01-19",
    bankTranscode: "TXN2024011905",
    transactionDescription: "Interest Credit",
    transactionType: "Credit",
    debitAmountRM: "-",
    creditAmountRM: "15.25",
    balanceRM: "11899.45",
    fpxNo: "-",
    paymentDetails: "Monthly savings account interest",
    bankRemark: "Interest credited"
  }
]);

// Utility functions for formatting
const formatCurrency = (amount) => {
  if (!amount || amount === '-') return '-';
  return new Intl.NumberFormat('en-MY', {
    style: 'currency',
    currency: 'MYR',
    minimumFractionDigits: 2
  }).format(parseFloat(amount));
};

const formatDate = (dateString) => {
  if (!dateString) return '-';
  return new Date(dateString).toLocaleDateString('en-MY', {
    year: 'numeric',
    month: 'short', 
    day: '2-digit'
  });
};

// Helper function to get file type from filename
const getFileTypeFromName = (fileName) => {
  if (!fileName) return '';
  const extension = fileName.split('.').pop().toLowerCase();
  const typeMap = {
    'csv': 'CSV (OTHERS)',
    'txt': 'TXT (Delimited)',
    'xlsx': 'Excel (XLSX)',
    'pdf': 'PDF Document'
  };
  return typeMap[extension] || 'Unknown Format';
};
</script>

<style lang="scss" scoped>
// Custom styles for the cash book page
.table-wrapper {
  .table-content {
    font-size: 0.9rem;
    
    th {
      font-weight: 600;
      font-size: 0.85rem;
    }
    
    td {
      vertical-align: middle;
    }
  }
}

// Upload form styling
:deep(.formkit-outer) {
  margin-bottom: 1.5rem;
}

:deep(.formkit-help) {
  font-size: 0.85rem;
  color: rgb(var(--text-color-secondary));
}

:deep(.formkit-label) {
  font-weight: 600;
  margin-bottom: 0.5rem;
}

// Readonly field styling
:deep(.formkit-input[readonly]) {
  background-color: rgb(var(--bg-1));
  color: rgb(var(--text-color-secondary));
  cursor: not-allowed;
  border-color: rgb(var(--border-color));
}

:deep(.formkit-input[disabled]) {
  background-color: rgb(var(--bg-1));
  color: rgb(var(--text-color-secondary));
  cursor: not-allowed;
  opacity: 0.7;
}

// Upload button styling
.rs-button[disabled] {
  opacity: 0.6;
  cursor: not-allowed;
}

// Card spacing
.rs-card + .rs-card {
  margin-top: 1.5rem;
}
</style>