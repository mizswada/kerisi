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
            <rs-button type="button" variant="info" @click="previewFile" :disabled="!formData.bankStatementFile">
              <Icon name="ic:outline-preview" class="mr-2" size="1rem" />
              Preview File
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
            <rs-button type="button" variant="info" @click="previewFile" :disabled="!formData.bankStatementAIFile">
              <Icon name="ic:outline-preview" class="mr-2" size="1rem" />
              Preview File
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

    <!-- Data Selection Modal -->
    <rs-modal v-model="showDataSelectionModal" size="full" :closable="false">
      <template #header>
        <div class="flex justify-between items-center">
          <h2 class="text-xl font-semibold">Select Data to Import</h2>
          <div class="text-sm text-gray-600">
            <Icon name="ic:outline-description" class="mr-1" size="1rem" />
            {{ uploadedFileName }}
          </div>
        </div>
      </template>
       <template #body>
         <div class="p-4">
           <div class="mb-4 flex justify-between items-center">
             <div class="flex items-center space-x-4">
               <label class="flex items-center">
                 <input
                   type="checkbox"
                   v-model="selectAll"
                   @change="toggleSelectAll"
                   class="rounded border-gray-300 text-blue-600 focus:ring-blue-500"
                 />
                 <span class="ml-2 text-sm font-medium text-gray-700">Select All</span>
               </label>
               <span class="text-sm text-gray-500">
                 {{ selectedTransactions.length }} of {{ bankStatementData.length }} selected
               </span>
             </div>
             <div class="flex space-x-2">
               <rs-button variant="secondary" size="sm" @click="clearSelection">
                 Clear Selection
               </rs-button>
             </div>
           </div>

           <!-- Data Table with Checkboxes -->
            <div class="overflow-x-auto">
              <table class="min-w-full bg-white border border-gray-200">
                <thead class="bg-gray-50">
                  <tr>
                    <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider border-b">
                      <input
                        type="checkbox"
                        v-model="selectAll"
                        @change="toggleSelectAll"
                        class="rounded border-gray-300 text-blue-600 focus:ring-blue-500"
                      />
                    </th>
                    <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider border-b">Transaction ID</th>
                    <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider border-b">Date</th>
                    <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider border-b">Account Number</th>
                    <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider border-b">Branch Code</th>
                    <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider border-b">Amount</th>
                  </tr>
                </thead>
                <tbody class="bg-white divide-y divide-gray-200">
                  <tr v-for="(row, index) in bankStatementData" :key="index" class="hover:bg-gray-50">
                    <td class="px-4 py-3 whitespace-nowrap border-b">
                      <input
                        type="checkbox"
                        :checked="selectedIndices.includes(index)"
                        @change="updateSelection(index)"
                        class="rounded border-gray-300 text-blue-600 focus:ring-blue-500"
                      />
                    </td>
                    <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-900 border-b">{{ row['Transaction ID'] }}</td>
                    <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-900 border-b">{{ row['Date'] }}</td>
                    <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-900 border-b">{{ row['Account Number'] }}</td>
                    <td class="px-4 py-3 whitespace-nowrap text-sm text-gray-900 border-b">{{ row['Branch Code'] }}</td>
                    <td class="px-4 py-3 whitespace-nowrap text-sm font-medium text-blue-600 border-b">
                      {{ formatCurrency(row['Amount']) }}
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
         </div>
       </template>
      <template #footer>
        <div class="flex justify-end space-x-3">
          <rs-button variant="secondary" @click="cancelDataSelection">
            Cancel
          </rs-button>
          <rs-button variant="primary" @click="confirmDataSelection" :disabled="selectedTransactions.length === 0">
            <Icon name="ic:outline-check-circle" class="mr-2" size="1rem" />
            CONFIRM ({{ selectedTransactions.length }} selected)
          </rs-button>
        </div>
      </template>
    </rs-modal>

    <!-- File Preview Modal -->
    <rs-modal v-model="showPreviewModal" size="6xl" :closable="true">
      <template #header>
        <div class="flex justify-between items-center">
          <h2 class="text-xl font-semibold">File Preview</h2>
          <div class="text-sm text-gray-600">
            <Icon name="ic:outline-description" class="mr-1" size="1rem" />
            {{ previewFileName }}
          </div>
        </div>
      </template>
      <template #body>
        <div class="p-4">
          <div class="mb-4">
            <div class="grid grid-cols-2 gap-4 mb-4">
              <div class="bg-blue-50 p-3 rounded-lg">
                <div class="flex items-center">
                  <Icon name="ic:outline-description" class="mr-2 text-blue-600" size="1.25rem" />
                  <div>
                    <h4 class="font-semibold text-blue-800">File Name</h4>
                    <p class="font-mono text-blue-700 text-sm">{{ previewFileName }}</p>
                  </div>
                </div>
              </div>
              <div class="bg-green-50 p-3 rounded-lg">
                <div class="flex items-center">
                  <Icon name="ic:outline-category" class="mr-2 text-green-600" size="1.25rem" />
                  <div>
                    <h4 class="font-semibold text-green-800">File Type</h4>
                    <p class="font-mono text-green-700 text-sm">{{ previewFileType }}</p>
                  </div>
                </div>
              </div>
            </div>
            <p class="text-sm text-gray-600 mb-2">
              This is a preview of your file content. Please review before proceeding with the upload.
            </p>
          </div>
          
          <!-- File Content Display -->
          <div class="bg-gray-50 border border-gray-200 rounded-lg p-4">
            <div class="max-h-96 overflow-y-auto">
              <!-- Show image for PDF files, text for other files -->
              <div v-if="previewFileContent === 'IMAGE_PREVIEW'" class="text-center">
                <img 
                  src="/img/screenshot.png" 
                  alt="PDF Preview Screenshot" 
                  class="max-w-full h-auto mx-auto rounded-lg shadow-lg"
                  style="max-height: 400px;"
                />
                <p class="text-sm text-gray-600 mt-3">
                  This is a preview screenshot of your PDF document. The actual file will be processed during upload.
                </p>
              </div>
              <pre v-else class="text-sm text-gray-800 whitespace-pre-wrap font-mono">{{ previewFileContent }}</pre>
            </div>
          </div>
          
          <div class="mt-4 p-3 bg-blue-50 rounded-lg">
            <div class="flex items-center">
              <Icon name="ic:outline-info" class="mr-2 text-blue-600" size="1.25rem" />
              <div>
                <h4 class="font-semibold text-blue-800">Preview Information</h4>
                <p class="text-blue-700 text-sm mt-1">
                  This preview shows the raw content of your file. After upload, the system will process and format this data for better display.
                  {{ previewFileType === 'PDF Document' ? 'PDF files will be processed using AI technology.' : '' }}
                </p>
              </div>
            </div>
          </div>
        </div>
      </template>
      <template #footer>
        <div class="flex justify-end space-x-3">
          <rs-button variant="secondary" @click="closePreviewModal">
            Close
          </rs-button>
          <rs-button variant="primary" @click="closePreviewModal">
            <Icon name="ic:outline-check-circle" class="mr-2" size="1rem" />
            Continue with Upload
          </rs-button>
        </div>
      </template>
    </rs-modal>

    <!-- Bank Statement Data Table Card (shown after selection) -->
    <rs-card v-if="isDocumentUploaded && !showDataSelectionModal">
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
          :data="selectedTransactions"
          :fieldLabels="fieldLabels"
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
          <!-- Custom formatting for Amount column -->
          <template #Amount="{ text }">
            <span class="font-medium text-blue-600">
              {{ formatCurrency(text) }}
            </span>
          </template>
          
          <!-- Balance formatting -->
          <template #Balance="{ text }">
            <span class="font-medium" :class="text.includes('+') ? 'text-green-600' : 'text-red-600'">
              {{ text }}
            </span>
          </template>
          
          <!-- Transaction Type Badge -->
          <template #Transaction_Type="{ text }">
            <rs-badge 
              :variant="text.includes('CR') || text.includes('Credit') ? 'success' : text.includes('Debit') ? 'danger' : 'secondary'"
              size="sm"
            >
              {{ text }}
            </rs-badge>
          </template>
          
          <!-- Date formatting -->
          <template #Date="{ text }">
            <span class="text-sm">{{ text }}</span>
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

// Modal and selection management
const showDataSelectionModal = ref(false);
const selectAll = ref(false);
const selectedTransactions = ref([]);

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
    
    // Reset selection state
    selectAll.value = false;
    selectedTransactions.value = [];
    selectedIndices.value = [];
    
    // Show success message and then open data selection modal
    await $swal.fire({
      title: 'Success!',
      text: `Document has been processed successfully. Please select the transactions you want to import.`,
      icon: 'success',
      confirmButtonText: 'OK'
    });
    
    // Open the data selection modal
    showDataSelectionModal.value = true;
    
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
      formData.value.bankStatementFile = file; // Store the file object directly, not in an array
      console.log('Normal File selected:', file.name);
    }
    
    // Validate file type
    const allowedTypes = ['.csv', '.txt', '.xlsx', '.pdf'];
    const fileExtension = '.' + file.name.split('.').pop().toLowerCase();
    
    if (!allowedTypes.includes(fileExtension)) {
      if ($swal) {
        $swal.fire({
          title: 'Invalid File Type!',
          text: 'Please select a CSV, TXT, XLSX, or PDF file.',
          icon: 'error',
          confirmButtonText: 'OK'
        });
      } else {
        alert('Invalid File Type! Please select a CSV, TXT, XLSX, or PDF file.');
      }
      
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
  
  // Reset modal and selection state
  showDataSelectionModal.value = false;
  selectAll.value = false;
  selectedTransactions.value = [];
  selectedIndices.value = [];
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

// Table headers matching the new data structure
const tableHeaders = [
  'Transaction ID',
  'Date', 
  'Account Number',
  'Branch Code',
  'Transaction Type',
  'Amount',
  'Balance',
  'Description',
  'Receiver'
];

// Table headers with checkbox column
const tableHeadersWithCheckbox = [
  'Select',
  'Transaction ID',
  'Date', 
  'Account Number',
  'Branch Code',
  'Transaction Type',
  'Amount',
  'Balance',
  'Description',
  'Receiver'
];

// Field labels for display
const fieldLabels = {
  'Select': 'Select',
  'Transaction ID': 'Transaction ID',
  'Date': 'Date',
  'Account Number': 'Account Number',
  'Branch Code': 'Branch Code',
  'Transaction Type': 'Transaction Type',
  'Amount': 'Amount',
  'Balance': 'Balance',
  'Description': 'Description',
  'Receiver': 'Receiver'
};

// Computed property to map data correctly for the table
const mappedTableData = computed(() => {
  return bankStatementData.value.map((item, index) => ({
    'Select': index, // Use index for checkbox
    'Transaction ID': item['Transaction ID'],
    'Date': item['Date'],
    'Account Number': item['Account Number'],
    'Branch Code': item['Branch Code'],
    'Transaction Type': item['Transaction Type'],
    'Amount': item['Amount'],
    'Balance': item['Balance'],
    'Description': item['Description'],
    'Receiver': item['Receiver']
  }));
});

// Bank statement data with new structure
const bankStatementData = ref([
  { 'Transaction ID': '000001', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9047', 'Transaction Type': 'IB FPX (CR) - CASA', 'Amount': '1500.00', 'Balance': '955478.59+', 'Description': '', 'Receiver': 'SAU EMPIRE RESOURCES' },
  { 'Transaction ID': '000002', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9047', 'Transaction Type': 'IB FPX (CR) - CASA', 'Amount': '0.50', 'Balance': '955478.09+', 'Description': '', 'Receiver': 'SAU EMPIRE RESOURCES' },
  { 'Transaction ID': '000003', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '100.00', 'Balance': '955578.09+', 'Description': 'Fund Transfer', 'Receiver': 'RADIN ALANG ISKANDAR' },
  { 'Transaction ID': '000004', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '1075.90', 'Balance': '956653.99+', 'Description': 'POTONGAN ZAKAT ALME NIAGA SDN. BHD.', 'Receiver': 'ALME NIAGA SDN. BHD.' },
  { 'Transaction ID': '000005', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1533', 'Transaction Type': 'CA CREDIT ADVICE', 'Amount': '2488.49', 'Balance': '959142.48+', 'Description': 'CC 01012025 PYMT SETT (38600900002)', 'Receiver': 'MAJLIS AGAMA ISLAM D' },
  { 'Transaction ID': '000006', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1533', 'Transaction Type': 'CA CREDIT ADVICE', 'Amount': '3563.08', 'Balance': '962705.56+', 'Description': 'CC 01012025 PYMT SETT (38600900001)', 'Receiver': 'MAJLIS AGAMA ISLAM D' },
  { 'Transaction ID': '000007', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1533', 'Transaction Type': 'CA CREDIT ADVICE', 'Amount': '41846.71', 'Balance': '1004552.27+', 'Description': 'CC 01012025 PYMT SETT (38600900002)', 'Receiver': 'MAJLIS AGAMA ISLAM D' },
  { 'Transaction ID': '000008', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1533', 'Transaction Type': 'CA CREDIT ADVICE', 'Amount': '44627.74', 'Balance': '1049180.01+', 'Description': 'CC 01012025 PYMT SETT (38600900001)', 'Receiver': 'MAJLIS AGAMA ISLAM D' },
  { 'Transaction ID': '000009', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '500.00', 'Balance': '1049680.01+', 'Description': 'ZAKAT', 'Receiver': 'HEZERIHISYAM BIN SAM' },
  { 'Transaction ID': '000010', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '300.00', 'Balance': '1049980.01+', 'Description': 'POTONGAN ZAKAT IFMAL TRADE SDN. BHD.', 'Receiver': 'IFMAL TRADE SDN BHD' },
  { 'Transaction ID': '000011', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '10.00', 'Balance': '1049990.01+', 'Description': 'Zakat', 'Receiver': 'MOHD PETRA BIN SERI' },
  { 'Transaction ID': '000012', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '2600.00', 'Balance': '1052590.01+', 'Description': 'Zakat simpanan 2024', 'Receiver': 'MAS LAILI BINTI SAAR' },
  { 'Transaction ID': '000013', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '6000.00', 'Balance': '1058590.01+', 'Description': 'Zakat 2024', 'Receiver': 'MOHD HAFIZ AHMAD' },
  { 'Transaction ID': '000014', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '250.00', 'Balance': '1058840.01+', 'Description': 'Jan 2025', 'Receiver': 'MUHAMAD AZMAN BIN AB' },
  { 'Transaction ID': '000015', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '900.00', 'Balance': '1059740.01+', 'Description': 'ZAKAT 12/2024', 'Receiver': 'DUTA AMAN PLT' },
  { 'Transaction ID': '000016', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '860.00', 'Balance': '1060600.01+', 'Description': 'Zakat', 'Receiver': 'NURUL AFIQAH BINTI M' },
  { 'Transaction ID': '001132', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1590', 'Transaction Type': 'IBG TRANSFER TO CA', 'Amount': '203.00', 'Balance': '24173557.24+', 'Description': 'G-94653-4781 ZKMAIPS', 'Receiver': 'RAZER MERCHANT SERVI' },
  { 'Transaction ID': '001133', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1590', 'Transaction Type': 'IBG TRANSFER TO CA', 'Amount': '22.00', 'Balance': '24173579.24+', 'Description': 'RPP-94653-1551 ZKMAIPS', 'Receiver': 'RAZER MERCHANT SERVI' },
  { 'Transaction ID': '001134', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '1590', 'Transaction Type': 'IBG TRANSFER TO CA', 'Amount': '36544.22', 'Balance': '24210123.46+', 'Description': 'ZKmaips -', 'Receiver': 'RAZER MERCHANT SERVICES SDN. BHD.' },
  { 'Transaction ID': '001135', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '1000.00', 'Balance': '24211123.46+', 'Description': 'Zakat 0125', 'Receiver': 'SYAIRUL BIN SULAIMAN' },
  { 'Transaction ID': '001136', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '765.00', 'Balance': '24211888.46+', 'Description': 'Zakat Pendptan 731020086171', 'Receiver': 'MOHD REDZAN BIN CHE' },
  { 'Transaction ID': '001137', 'Date': '01/01/2025', 'Account Number': '09010010055290', 'Branch Code': '9895', 'Transaction Type': 'INW DuitNow Transfer', 'Amount': '500.00', 'Balance': '24212388.46+', 'Description': 'Zakat 2024 Muhammad Hezri', 'Receiver': 'SYUHAIDA BINTI MUHAM' },
  { 'Transaction ID': '001138', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '0144', 'Transaction Type': 'TRANSFER TO CA', 'Amount': '24128009.91', 'Balance': '84378.55+', 'Description': '9010010000575', 'Receiver': 'MAJLIS AGAMA ISLAM DAN ADAT ISTIADAT MEL' },
  { 'Transaction ID': '001139', 'Date': '31/01/2025', 'Account Number': '09010010055290', 'Branch Code': '0160', 'Transaction Type': 'PROFIT PAID', 'Amount': '4718.29', 'Balance': '89096.84+', 'Description': '', 'Receiver': '' }
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
  // Handle DD/MM/YYYY format
  if (dateString.includes('/')) {
    const [day, month, year] = dateString.split('/');
    return new Date(year, month - 1, day).toLocaleDateString('en-MY', {
      year: 'numeric',
      month: 'short', 
      day: '2-digit'
    });
  }
  // Handle ISO date format
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

// Track selected indices instead of modifying objects
const selectedIndices = ref([]);



// Selection management functions
const toggleSelectAll = () => {
  if (selectAll.value) {
    // Select all
    selectedIndices.value = bankStatementData.value.map((_, index) => index);
    selectedTransactions.value = [...bankStatementData.value];
  } else {
    // Deselect all
    selectedIndices.value = [];
    selectedTransactions.value = [];
  }
};

const updateSelection = (index) => {
  if (selectedIndices.value.includes(index)) {
    // Remove from selection
    selectedIndices.value = selectedIndices.value.filter(i => i !== index);
  } else {
    // Add to selection
    selectedIndices.value.push(index);
  }
  
  // Update selected transactions
  selectedTransactions.value = selectedIndices.value.map(i => bankStatementData.value[i]);
  
  // Update select all state
  selectAll.value = selectedIndices.value.length === bankStatementData.value.length;
};

const clearSelection = () => {
  selectAll.value = false;
  selectedIndices.value = [];
  selectedTransactions.value = [];
};

const confirmDataSelection = () => {
  if (selectedTransactions.value.length === 0) {
    $swal.fire({
      title: 'No Transactions Selected!',
      text: 'Please select at least one transaction to continue.',
      icon: 'warning',
      confirmButtonText: 'OK'
    });
    return;
  }
  
  // Close modal and show selected data
  showDataSelectionModal.value = false;
  
  // Show success message
  $swal.fire({
    title: 'Data Selected Successfully!',
    text: `${selectedTransactions.value.length} transactions have been selected and will be displayed.`,
    icon: 'success',
    confirmButtonText: 'OK'
  });
};

const cancelDataSelection = () => {
  // Reset selection and close modal
  clearSelection();
  showDataSelectionModal.value = false;
  isDocumentUploaded.value = false;
  selectedIndices.value = [];
};

// Preview file functionality
const showPreviewModal = ref(false);
const previewFileContent = ref('');
const previewFileName = ref('');
const previewFileType = ref('');

const previewFile = () => {
  console.log('previewFile function called');
  console.log('isAIUpload.value:', isAIUpload.value);
  console.log('formData.value:', formData.value);
  
  let file = null;
  
  if (isAIUpload.value) {
    file = formData.value.bankStatementAIFile;
    console.log('AI file:', file);
  } else {
    file = formData.value.bankStatementFile;
    console.log('Normal file:', file);
  }
  
  // Handle case where file might be in an array (FormKit sometimes returns arrays)
  if (Array.isArray(file)) {
    file = file[0]; // Take the first file if it's an array
    console.log('File was in array, taking first element:', file);
  }
  
  if (!file) {
    console.log('No file selected, showing warning');
    if ($swal) {
      $swal.fire({
        title: 'No File Selected!',
        text: 'Please select a file to preview.',
        icon: 'warning',
        confirmButtonText: 'OK'
      });
    } else {
      alert('No File Selected! Please select a file to preview.');
    }
    return;
  }
  
  console.log('File selected:', file.name);
  previewFileName.value = file.name;
  previewFileType.value = getFileTypeFromName(file.name);
  
  // Handle different file types
  if (file.name.toLowerCase().endsWith('.pdf')) {
    console.log('PDF file detected');
    // For PDF files, show the screenshot image
    previewFileContent.value = 'IMAGE_PREVIEW'; // Special flag to indicate image preview
    showPreviewModal.value = true;
    console.log('Modal should be shown, showPreviewModal.value:', showPreviewModal.value);
  } else if (file.name.toLowerCase().endsWith('.xlsx') || file.name.toLowerCase().endsWith('.xls')) {
    console.log('Excel file detected');
    // For Excel files, show a message
    previewFileContent.value = 'Excel file preview shows raw data. The file will be properly formatted during upload.';
    showPreviewModal.value = true;
    console.log('Modal should be shown, showPreviewModal.value:', showPreviewModal.value);
  } else {
    console.log('Text file detected');
    // For CSV and TXT files, read and show the content
    const reader = new FileReader();
    
    reader.onload = (event) => {
      console.log('File read successfully');
      previewFileContent.value = event.target.result;
      showPreviewModal.value = true;
      console.log('Modal should be shown, showPreviewModal.value:', showPreviewModal.value);
    };
    
    reader.onerror = () => {
      console.log('File read error');
      if ($swal) {
        $swal.fire({
          title: 'Preview Error!',
          text: 'Unable to read file content. Please try again.',
          icon: 'error',
          confirmButtonText: 'OK'
        });
      } else {
        alert('Preview Error! Unable to read file content. Please try again.');
      }
    };
    
    // Read text-based files
    reader.readAsText(file);
  }
};

const closePreviewModal = () => {
  showPreviewModal.value = false;
  previewFileContent.value = '';
  previewFileName.value = '';
  previewFileType.value = '';
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