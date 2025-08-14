<template>
  <div>
    <LayoutsBreadcrumb />
    
    <!-- Warning Alert -->
    <rs-alert variant="danger" class="mb-6">
      <Icon name="ic:outline-warning" size="20" class="mr-2" />
      Don't use this screen to claim on behalf of others
    </rs-alert>

    <!-- Travel Claim Form -->
    <rs-card>
      <template #header>
        <div class="flex items-center justify-between">
          <h2 class="text-xl font-semibold">Travelling Claim Details</h2>
          <div class="flex gap-2">
            <Icon name="ic:outline-numbers" size="20" />
            <Icon name="ic:outline-content-copy" size="20" />
            <Icon name="ic:outline-edit" size="20" />
          </div>
        </div>
      </template>
      
      <template #body>
        <FormKit type="form" @submit="submitForm">
          <!-- Staff Details Grid -->
          <FormKit
                    type="text"
                    label="Staff Name"
                    v-model="formData.staffName"
                    value="SYSTEM ADMIN"
                    readonly
                    :classes="{
                    input: 'bg-gray-100 cursor-not-allowed'
                    }"
                />
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
                <!-- Left Column -->
                <div class="space-y-4">
                
                
                <FormKit
                    type="text"
                    label="Staff No"
                    v-model="formData.staffNo"
                    value="42"
                    readonly
                    :classes="{
                    input: 'bg-gray-100 cursor-not-allowed'
                    }"
                />
                
                <FormKit
                    type="select"
                    name="staffPtj"
                    label="Staff PTJ"
                    v-model="formData.staffPtj"
                    :options="ptjOptions"
                    placeholder="Select PTJ"
                    validation="required"
                />
                
                <FormKit
                    type="select"
                    label="Transport Reg No"
                    v-model="formData.transportRegNo"
                    :options="transportOptionsWithAddNew"
                    placeholder="Select Transport"
                    value="KCF966"
                    @input="handleTransportRegNoChange"
                />
                
                <FormKit
                    type="select"
                    name="claimMonth"
                    label="Claim For Month"
                    v-model="formData.claimMonth"
                    :options="monthOptions"
                    placeholder="Select Month"
                    validation="required"
                    value="June"
                />
                
                <FormKit
                    type="select"
                    name="claimYear"
                    label="Claim Year"
                    v-model="formData.claimYear"
                    :options="yearOptions"
                    placeholder="Select Year"
                    validation="required"
                    value="2025"
                />
                </div>
                
            <!-- Right Column -->
                <div class="space-y-4">
                <FormKit
                    type="text"
                    label="Application No"
                    v-model="formData.applicationNo"
                    readonly
                    :classes="{
                    input: 'bg-gray-100 cursor-not-allowed'
                    }"
                />
                
                <FormKit
                    type="text"
                    label="Application Status"
                    v-model="formData.applicationStatus"
                    value="DRAFT"
                    readonly
                    :classes="{
                    input: 'bg-gray-100 cursor-not-allowed'
                    }"
                />
                
                <FormKit
                    type="text"
                    label="Bill No"
                    v-model="formData.billNo"
                    readonly
                    :classes="{
                    input: 'bg-gray-100 cursor-not-allowed'
                    }"
                />
                
                <FormKit
                    type="text"
                    label="Journal No"
                    v-model="formData.journalNo"
                    readonly
                    :classes="{
                    input: 'bg-gray-100 cursor-not-allowed'
                    }"
                />
                </div>
            </div>

          <!-- Claim Item Section -->
          <div class="mb-6">
            <h3 class="text-lg font-semibold mb-4">Claim Item</h3>
            
            <!-- Claim Items Table -->
            <div class="bg-white border border-gray-200 rounded-lg overflow-hidden">
              <!-- Table Header -->
              <div class="bg-blue-400 text-white">
                <div class="grid grid-cols-7 gap-4 p-4 text-sm font-medium">
                  <div>Date From</div>
                  <div>Date To</div>
                  <div>Item</div>
                  <div>Description</div>
                  <div>Mileage (KM)</div>
                  <div>Amount (RM)</div>
                  <div>Action</div>
                </div>
              </div>
              
              <!-- Table Body -->
              <div v-if="claimItems.length === 0" class="p-8 text-center text-gray-500">
                No records
              </div>
              
              <div v-else>
                <div 
                  v-for="(item, index) in claimItems" 
                  :key="index"
                  class="grid grid-cols-7 gap-4 p-4 border-b border-gray-200 hover:bg-gray-50"
                >
                  <div>{{ item.dateFrom }}</div>
                  <div>{{ item.dateTo }}</div>
                  <div>{{ item.item }}</div>
                  <div>{{ item.description }}</div>
                  <div class="text-right">{{ item.mileage }}</div>
                  <div class="text-right">{{ item.amount }}</div>
                  <div>
                    <rs-button 
                      variant="danger" 
                      size="sm" 
                      @click="deleteItem(index)"
                    >
                      Delete
                    </rs-button>
                  </div>
                </div>
              </div>
              
              <!-- Table Action Buttons -->
              <div class="p-4 bg-gray-50 border-t border-gray-200">
                <div class="flex justify-end gap-2">
                  <rs-button 
                    variant="danger-outline" 
                    size="sm"
                    @click="deleteAllItems"
                    :disabled="claimItems.length === 0"
                  >
                    <Icon name="ic:outline-delete" class="mr-1" size="16" />
                    Delete All
                  </rs-button>
                  
                  <rs-button 
                    variant="primary-outline" 
                    size="sm"
                    @click="addItem"
                  >
                    <Icon name="ic:outline-add" class="mr-1" size="16" />
                    Add
                  </rs-button>
                  
                  <rs-button 
                    variant="info-outline" 
                    size="sm"
                    @click="addWithAI"
                  >
                    <Icon name="ic:outline-auto-awesome" class="mr-1" size="16" />
                    Add With AI
                  </rs-button>
                </div>
              </div>
            </div>
          </div>

          <!-- Form Action Buttons -->
          <div class="flex justify-center gap-4 pt-6 border-t border-gray-200">
            <rs-button 
              variant="secondary-outline"
              @click="printForm"
              type="button"
            >
              <Icon name="ic:outline-print" class="mr-2" size="16" />
              Print
            </rs-button>
            
            <rs-button 
              variant="info-outline"
              @click="saveForm"
              type="button"
            >
              <Icon name="ic:outline-save" class="mr-2" size="16" />
              Save
            </rs-button>
            
            <rs-button 
              variant="success"
              type="submit"
            >
              <Icon name="ic:outline-send" class="mr-2" size="16" />
              Submit
            </rs-button>
          </div>
        </FormKit>
      </template>
    </rs-card>

    <!-- Add New Transport Reg No Modal -->
    <rs-modal
      v-model="showTransportModal"
      title="Add New Transport Registration Number"
      size="md"
      :ok-callback="submitNewTransportRegNo"
      :cancel-callback="closeTransportModal"
      ok-title="Submit"
      cancel-title="Cancel"
      :overlayClose="false"
    >
      <template #body>
        <FormKit
          type="text"
          name="newTransportRegNo"
          label="Transport Reg No"
          v-model="newTransportRegNo"
          placeholder="Enter transport registration number"
          validation="required"
        />
      </template>
    </rs-modal>

    <!-- Add New Claim Item Modal -->
    <rs-modal
      v-model="showClaimItemModal"
      title="Claim Item"
      size="lg"
      :ok-callback="submitNewClaimItem"
      :cancel-callback="closeClaimItemModal"
      ok-title="Save"
      cancel-title="Cancel"
      :overlayClose="false"
    >
      <template #body>
        <div class="space-y-4">
          <FormKit
            type="select"
            name="claimItem"
            label="Item"
            v-model="newClaimItem.item"
            :options="claimItemOptions"
            placeholder="Select Item"
            validation="required"
          />
          
          <FormKit
            type="date"
            name="dateFrom"
            label="Date From"
            v-model="newClaimItem.dateFrom"
            validation="required"
          />
          
          <FormKit
            type="date"
            name="dateTo"
            label="Date To"
            v-model="newClaimItem.dateTo"
            validation="required"
          />
          
          <FormKit
            type="textarea"
            label="Description"
            v-model="newClaimItem.description"
            placeholder="Enter description"
            rows="3"
          />
          
          <FormKit
            type="number"
            name="amount"
            label="Amount"
            v-model="newClaimItem.amount"
            placeholder="0.00"
            step="0.01"
            min="0"
            validation="required"
            prefix-icon="RM"
          />
        </div>
      </template>
    </rs-modal>

    <!-- Add Claim Item With AI Modal -->
    <rs-modal
      v-model="showAIModal"
      title="Claim Item With AI"
      size="lg"
      :ok-callback="submitAIClaimItem"
      :cancel-callback="closeAIModal"
      ok-title="Save"
      cancel-title="Cancel"
      :overlayClose="false"
    >
      <template #body>
        <div class="space-y-4">
          <FormKit
            type="file"
            label="Upload File"
            v-model="aiClaimData.uploadedFiles"
            accept=".pdf,.jpg,.jpeg,.png,.doc,.docx"
            multiple
            help="Upload multiple receipts, invoices, or related documents"
          />
          
          <FormKit
            type="textarea"
            label="Prompt"
            v-model="aiClaimData.prompt"
            placeholder="Describe what you want to extract from the uploaded file or provide additional context for AI processing..."
            rows="6"
          />
        </div>
      </template>
    </rs-modal>
  </div>
</template>

<script setup>
definePageMeta({
  title: "Travel Claim",
  middleware: "auth"
});

// Get $swal from nuxt app
const { $swal } = useNuxtApp();

// Form data
const formData = ref({
  staffName: "Aizal Manan",
  staffNo: "42",
  staffPtj: "Marketing Department",
  transportRegNo: "KCF966",
  claimMonth: "June",
  claimYear: "2025",
  applicationNo: "CLAIM-2025-06",
  applicationStatus: "DRAFT",
  billNo: "",
  journalNo: ""
});

// Claim items array
const claimItems = ref([]);

// Modal state and new transport reg no
const showTransportModal = ref(false);
const newTransportRegNo = ref("");

// Claim item modal state
const showClaimItemModal = ref(false);
const newClaimItem = ref({
  item: "",
  dateFrom: "",
  dateTo: "",
  description: "",
  amount: ""
});

// AI modal state
const showAIModal = ref(false);
const aiClaimData = ref({
  uploadedFiles: [],
  prompt: ""
});

// Options for dropdowns
const ptjOptions = [
  "IT Department",
  "HR Department", 
  "Finance Department",
  "Marketing Department"
];

const transportOptions = ref([
  "KCF966",
  "ABC123",
  "XYZ789"
]);

// Computed property to add "Add New Transport Reg No" option
const transportOptionsWithAddNew = computed(() => [
  ...transportOptions.value,
  "Add New Transport Reg No"
]);

const monthOptions = [
  "January", "February", "March", "April", "May", "June",
  "July", "August", "September", "October", "November", "December"
];

const yearOptions = [
  "2023", "2024", "2025", "2026"
];

const claimItemOptions = [
  "Travel",
  "Accommodation", 
  "Meals",
  "Transport",
  "Fuel",
  "Parking",
  "Toll",
  "Others"
];

// Methods
// Handle transport reg no dropdown change
const handleTransportRegNoChange = (value) => {
  if (value === "Add New Transport Reg No") {
    // Reset the dropdown to previous value and open modal
    formData.value.transportRegNo = "KCF966"; // or keep the previous value
    showTransportModal.value = true;
  }
};

// Modal methods
const closeTransportModal = () => {
  showTransportModal.value = false;
  newTransportRegNo.value = "";
};

const submitNewTransportRegNo = () => {
  if (newTransportRegNo.value.trim()) {
    // Add to transport options
    transportOptions.value.push(newTransportRegNo.value.trim());
    
    // Set as selected value
    formData.value.transportRegNo = newTransportRegNo.value.trim();
    
    // Show success message
    $swal.fire({
      icon: 'success',
      title: 'Success!',
      text: 'Transport registration number has been added successfully.',
      timer: 2000,
      showConfirmButton: false
    });
    
    // Close modal and reset
    closeTransportModal();
  }
};

// Claim item modal methods
const closeClaimItemModal = () => {
  showClaimItemModal.value = false;
  resetClaimItemForm();
};

const resetClaimItemForm = () => {
  newClaimItem.value = {
    item: "",
    dateFrom: "",
    dateTo: "",
    description: "",
    amount: ""
  };
};

const submitNewClaimItem = () => {
  // Validate that required fields are filled
  if (newClaimItem.value.item && newClaimItem.value.dateFrom && newClaimItem.value.dateTo && newClaimItem.value.amount) {
    // Calculate mileage (you can modify this logic as needed)
    const mileage = newClaimItem.value.item === "Travel" ? "100" : "0";
    
    // Add to claim items array
    claimItems.value.push({
      dateFrom: newClaimItem.value.dateFrom,
      dateTo: newClaimItem.value.dateTo,
      item: newClaimItem.value.item,
      description: newClaimItem.value.description || "-",
      mileage: mileage,
      amount: parseFloat(newClaimItem.value.amount).toFixed(2)
    });
    
    // Show success message
    $swal.fire({
      icon: 'success',
      title: 'Success!',
      text: 'Claim item has been added successfully.',
      timer: 2000,
      showConfirmButton: false
    });
    
    // Close modal and reset form
    closeClaimItemModal();
  }
};

// AI modal methods
const closeAIModal = () => {
  showAIModal.value = false;
  resetAIForm();
};

const resetAIForm = () => {
  aiClaimData.value = {
    uploadedFiles: [],
    prompt: ""
  };
};

const submitAIClaimItem = async () => {
  try {
    // Check if files are uploaded
    if (!aiClaimData.value.uploadedFiles || aiClaimData.value.uploadedFiles.length === 0) {
      $swal.fire({
        icon: 'warning',
        title: 'No Files Selected',
        text: 'Please upload at least one file to process with AI.',
      });
      return;
    }

    const fileCount = aiClaimData.value.uploadedFiles.length;
    
    // Show processing message
    $swal.fire({
      title: 'Processing...',
      text: `AI is analyzing your ${fileCount} file(s) and generating claim items.`,
      allowOutsideClick: false,
      didOpen: () => {
        $swal.showLoading();
      }
    });

    // Simulate AI processing (replace with actual AI API call)
    await new Promise(resolve => setTimeout(resolve, 3000));
    
    // Mock AI-generated claim items based on number of files (replace with actual AI response)
    const aiGeneratedItems = [];
    
    // Generate items for each uploaded file
    for (let i = 0; i < fileCount; i++) {
      const fileIndex = i + 1;
      
      // Generate different types of items for each file
      if (i % 3 === 0) {
        aiGeneratedItems.push({
          dateFrom: `2025-06-07`,
          dateTo: `2025-06-07`,
          item: "Travel",
          description: `Business trip - AI extracted from file ${fileIndex}`,
          mileage: "150",
          amount: (65.81).toFixed(2)
        });
      } else if (i % 3 === 1) {
        aiGeneratedItems.push({
          dateFrom: `2025-06-07`,
          dateTo: `2025-06-08`,
          item: "Accommodation",
          description: `Hotel stay - AI extracted from file ${fileIndex}`,
          mileage: "0",
          amount: (85.91).toFixed(2)
        });
        
      } else {
        aiGeneratedItems.push({
          dateFrom: `2025-06-07`,
          dateTo: `2025-06-07`,
          item: "Meals",
          description: `Meal expense - AI extracted from file ${fileIndex}`,
          mileage: "0",
          amount: (25.00).toFixed(2)
        });
      }
    }
    
    // Add AI-generated items to claim items array
    claimItems.value.push(...aiGeneratedItems);
    
    // Show success message
    $swal.fire({
      icon: 'success',
      title: 'AI Processing Complete!',
      text: `${aiGeneratedItems.length} claim items have been generated from ${fileCount} file(s) and added successfully.`,
      timer: 3000,
      showConfirmButton: false
    });
    
    // Close modal and reset form
    closeAIModal();
    
  } catch (error) {
    console.error('AI processing error:', error);
    $swal.fire({
      icon: 'error',
      title: 'Processing Failed',
      text: 'Unable to process the files with AI. Please try again.',
    });
  }
};

const addItem = () => {
  // Open the claim item modal
  showClaimItemModal.value = true;
};

const addWithAI = () => {
  // Open the AI modal
  showAIModal.value = true;
};

const deleteItem = (index) => {
  claimItems.value.splice(index, 1);
};

const deleteAllItems = () => {
  if (confirm("Are you sure you want to delete all items?")) {
    claimItems.value = [];
  }
};

const printForm = () => {
  window.print();
};

const saveForm = () => {
  console.log("Saving form...", formData.value);
  // Save logic would go here
  $swal.fire({
    icon: 'success',
    title: 'Saved!',
    text: 'Travel claim has been saved as draft.',
  });
};

const submitForm = () => {
  console.log("Submitting form...", formData.value, claimItems.value);
  // Submit logic would go here
  $swal.fire({
    icon: 'success',
    title: 'Submitted!',
    text: 'Travel claim has been submitted successfully.',
  });
};
</script>