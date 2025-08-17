<script setup>
import { ref, computed, watch, nextTick } from 'vue';
import { marked } from 'marked';

const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false,
  },
  position: {
    type: String,
    default: 'bottom-right', // bottom-right, bottom-left, top-right, top-left
  },
  size: {
    type: String,
    default: 'md', // sm, md, lg
  },
  title: {
    type: String,
    default: 'AINA',
  },
  assistantName: {
    type: String,
    default: 'AINA',
  },
});

const emit = defineEmits(['update:modelValue', 'send-message']);

// Chat state - start with only the initial AINA message
const messages = ref([
  {
    id: 1,
    type: 'assistant',
    content: "Hi! I'm AINA, your Accounting & Navigation Intelligence Assistant. How can I help you today?",
    timestamp: new Date(),
  },
]);

const newMessage = ref('');
const chatContainer = ref(null);
const isLoading = ref(false);
const showMetadataModal = ref(false);
const metadataDetails = ref(null);

// Status mapping to store document statuses from status messages
const documentStatusMap = ref(new Map());

// API configuration
const API_BASE_URL = 'http://157.245.54.152:8000/chat';

// Position classes
const positionClasses = computed(() => {
  const positions = {
    'bottom-right': 'bottom-4 right-4',
    'bottom-left': 'bottom-4 left-4',
    'top-right': 'top-4 right-4',
    'top-left': 'top-4 left-4',
  };
  return positions[props.position] || positions['bottom-right'];
});

// Size classes
const sizeClasses = computed(() => {
  const sizes = {
    'sm': 'w-80 h-96',
    'md': 'w-96 h-[500px]',
    'lg': 'w-[450px] h-[600px]',
  };
  return sizes[props.size] || sizes['md'];
});

const closeModal = () => {
  emit('update:modelValue', false);
};

// Function to generate random bill numbers
const generateRandomBillNumbers = (count = 5) => {
  const bills = [];
  for (let i = 0; i < count; i++) {
    // Generate random 3-digit number for APN000***
    const randomNum = Math.floor(Math.random() * 900) + 100; // 100-999
    
    // Generate random year (24) and month (01-12)
    const year = '24';
    const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
    
    const billNumber = `APN000${randomNum}/${year}${month}`;
    bills.push(billNumber);
  }
  
  // Ensure we have at least 2 unique bills
  const uniqueBills = [...new Set(bills)];
  if (uniqueBills.length < 2) {
    // Add more bills if we don't have enough unique ones
    while (uniqueBills.length < 2) {
      const randomNum = Math.floor(Math.random() * 900) + 100;
      const year = '24';
      const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
      const billNumber = `APN000${randomNum}/${year}${month}`;
      if (!uniqueBills.includes(billNumber)) {
        uniqueBills.push(billNumber);
      }
    }
  }
  
  return uniqueBills;
};

// Function to generate random PO numbers
const generateRandomPONumbers = (count = 5) => {
  const pos = [];
  for (let i = 0; i < count; i++) {
    let poNumber;
    
    // Randomly choose between PRO***/**** and PO******** formats
    if (Math.random() > 0.5) {
      // Generate PRO***/**** format
      const randomNum = Math.floor(Math.random() * 900) + 100; // 100-999
      const year = '24';
      const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
      poNumber = `PRO${randomNum}/${year}${month}`;
    } else {
      // Generate PO******** format (like PO230500148)
      const year = '23'; // 2023
      const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
      const day = String(Math.floor(Math.random() * 31) + 1).padStart(2, '0');
      const sequence = String(Math.floor(Math.random() * 999) + 1).padStart(3, '0');
      poNumber = `PO${year}${month}${day}${sequence}`;
    }
    
    pos.push(poNumber);
  }
  
  // Ensure we have at least 2 unique POs
  const uniquePOs = [...new Set(pos)];
  if (uniquePOs.length < 2) {
    // Add more POs if we don't have enough unique ones
    while (uniquePOs.length < 2) {
      let poNumber;
      if (Math.random() > 0.5) {
        const randomNum = Math.floor(Math.random() * 900) + 100;
        const year = '24';
        const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
        poNumber = `PRO${randomNum}/${year}${month}`;
      } else {
        const year = '23';
        const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
        const day = String(Math.floor(Math.random() * 31) + 1).padStart(2, '0');
        const sequence = String(Math.floor(Math.random() * 999) + 1).padStart(3, '0');
        poNumber = `PO${year}${month}${day}${sequence}`;
      }
      
      if (!uniquePOs.includes(poNumber)) {
        uniquePOs.push(poNumber);
      }
    }
  }
  
  return uniquePOs;
};

// Function to generate realistic bill details
const generateBillDetails = (billNumber, status = null) => {
  // Extract month and year from bill number (APN000369/2402 -> month: 02, year: 24)
  const parts = billNumber.split('/');
  const monthYear = parts[1];
  const month = monthYear.substring(2, 4); // Get month (02)
  const year = monthYear.substring(0, 2); // Get year (24)
  
  // Generate random amounts between 1000 and 50000
  const amount = (Math.random() * 49000 + 1000).toFixed(2);
  const balance = amount; // Balance usually equals amount for new bills
  
  // Generate random dates based on the month/year from bill number
  const day = Math.floor(Math.random() * 28) + 1; // Random day 1-28
  const approveDate = `${day.toString().padStart(2, '0')}/${month}/${year}`;
  
  // Generate related numbers
  const grnWpnNo = `WPN${String(Math.floor(Math.random() * 999) + 1).padStart(3, '0')}/${month}${year}`;
  const invoiceNo = `#${String(Math.floor(Math.random() * 999) + 1).padStart(3, '0')}/${year}`;
  const invoiceDate = approveDate;
  
  // Sample descriptions for different types of services
  const descriptions = [
    "PERKHIDMATAN MENAIKTARAF SISTEM KEWANGAN MYFIS VERSI 2.0",
    "PERKHIDMATAN PENYELENGGARAAN SISTEM IT",
    "PERKHIDMATAN KONSULTASI KEWANGAN",
    "PERKHIDMATAN AUDIT INTERNAL",
    "PERKHIDMATAN LATIHAN KAKITANGAN",
    "PERKHIDMATAN PEMBANGUNAN APLIKASI",
    "PERKHIDMATAN PENYELENGGARAAN PERALATAN",
    "PERKHIDMATAN KESELAMATAN MAKLUMAT",
    "PERKHIDMATAN BACKUP DAN RECOVERY",
    "PERKHIDMATAN NETWORK INFRASTRUKTUR"
  ];
  
  const description = descriptions[Math.floor(Math.random() * descriptions.length)];
  
  // Use provided status or generate random one
  const finalStatus = status || ["APPROVE", "PENDING", "REJECT", "DRAFT"][Math.floor(Math.random() * 4)];
  
  return {
    "Approve Date": approveDate,
    "Bill No": billNumber,
    "GRN/WPN No": grnWpnNo,
    "Requisition No": Math.random() > 0.5 ? `REQ${String(Math.floor(Math.random() * 999) + 1).padStart(3, '0')}/${month}${year}` : "",
    "Description": description,
    "Invoice No": invoiceNo,
    "Invoice Date": invoiceDate,
    "Amount": amount,
    "Balance": balance,
    "Status": finalStatus
  };
};

 // Function to generate realistic PO details
 const generatePODetails = (poNumber, status = null) => {
   let month, year, day;
   
   // Check if it's PRO***/**** format or PO******** format
   if (poNumber.includes('/')) {
     // PRO***/**** format (e.g., PRO097/2408)
     const parts = poNumber.split('/');
     const monthYear = parts[1];
     month = monthYear.substring(2, 4); // Get month (08)
     year = monthYear.substring(0, 2); // Get year (24)
     day = Math.floor(Math.random() * 28) + 1; // Random day 1-28
   } else {
     // PO******** format (e.g., PO230500148)
     month = poNumber.substring(4, 6); // Get month (05)
     year = poNumber.substring(2, 4); // Get year (23)
     day = poNumber.substring(6, 8); // Get day (00)
   }
   
   // Generate random amounts between 1000 and 100000
   const amount = (Math.random() * 99000 + 1000).toFixed(1);
   
   // Generate random dates based on the month/year from PO number
   const approveDate = `${day.toString().padStart(2, '0')}/${month}/${year}`;
   
   // Sample descriptions for different types of services
   const descriptions = [
     "PERUBAHAN SISTEM APLIKASI",
     "PERMOHONAN PERUBAHAN SISTEM APLIKASI",
     "MEMPERBAHARUI PENYELENGGARAAN SISTEM KEWANGAN MYFIS DI LPPM BAGI TEMPOH (17.06.2023 - 16.12.2023)",
     "PEMBANGUNAN SISTEM APLIKASI BARU",
     "PENYELENGGARAAN SISTEM IT",
     "UPGRADE SISTEM KEWANGAN",
     "PERUBAHAN INFRASTRUKTUR IT",
     "PEMBAHARUAN SISTEM DATABASE",
     "PEMASANGAN PERALATAN IT",
     "KONSULTASI SISTEM KEWANGAN"
   ];
   
   const description = descriptions[Math.floor(Math.random() * descriptions.length)];
   
   // Use provided status or default to APPROVE
   const finalStatus = status || "APPROVE";
   
   return {
     "Date": approveDate,
     "PO Number": poNumber,
     "Description": description,
     "Amount": amount,
     "Status": finalStatus
   };
 };

 // Function to generate realistic invoice details
 const generateInvoiceDetails = (invoiceNumber) => {
   // Extract month and year from invoice number (INV0124001 -> month: 01, year: 24)
   const month = invoiceNumber.substring(3, 5); // Get month (01)
   const year = invoiceNumber.substring(5, 7); // Get year (24)
   
   // Generate random day
   const day = Math.floor(Math.random() * 28) + 1;
   const invoiceDate = `${day.toString().padStart(2, '0')}/${month}/${year}`;
   
   // Generate random amounts between 1000 and 50000
   const amount = (Math.random() * 49000 + 1000).toFixed(2);
   const taxAmount = (parseFloat(amount) * 0.06).toFixed(2); // 6% SST
   const totalAmount = (parseFloat(amount) + parseFloat(taxAmount)).toFixed(2);
   
   // Generate related numbers
   const poNumber = `PO${year}${month}${String(Math.floor(Math.random() * 31) + 1).padStart(2, '0')}${String(Math.floor(Math.random() * 999) + 1).padStart(3, '0')}`;
   const customerRef = `CUST${String(Math.floor(Math.random() * 999) + 1).padStart(3, '0')}`;
   
   // Sample descriptions for different types of services
   const descriptions = [
     "PERKHIDMATAN SISTEM IT",
     "PERKHIDMATAN KONSULTASI KEWANGAN",
     "PERKHIDMATAN AUDIT INTERNAL",
     "PERKHIDMATAN LATIHAN KAKITANGAN",
     "PERKHIDMATAN PEMBANGUNAN APLIKASI",
     "PERKHIDMATAN PENYELENGGARAAN PERALATAN",
     "PERKHIDMATAN KESELAMATAN MAKLUMAT",
     "PERKHIDMATAN BACKUP DAN RECOVERY"
   ];
   
   const description = descriptions[Math.floor(Math.random() * descriptions.length)];
   
   return {
     "Invoice No": invoiceNumber,
     "Invoice Date": invoiceDate,
     "PO Number": poNumber,
     "Customer Ref": customerRef,
     "Description": description,
     "Subtotal": amount,
     "SST (6%)": taxAmount,
     "Total Amount": totalAmount,
     "Status": "PAID"
   };
 };

 // Function to linkify IDs in HTML content
 const linkifyIDs = (html) => {
   // Regex patterns for each type (adjust as needed)
   const patterns = [
     { type: 'po', regex: /(PO\d{6,}|PRO\d{3}\/\d{4})/g },
     { type: 'bill', regex: /(APN\d{6}\/\d{4}|IR\d{9,})/g },
     { type: 'invoice', regex: /(INV\d{6,})/g },
     { type: 'voucher', regex: /(VCR\d{6,}\/\d{4})/g },
     { type: 'payment', regex: /(EFT\d{5,}\/\d{4})/g }
   ];
   
   patterns.forEach(({type, regex}) => {
     html = html.replace(regex, match =>
       `<span class="id-link cursor-pointer text-blue-600 hover:text-blue-800 underline" data-type="${type}" data-id="${match}">${match}</span>`
     );
   });
   return html;
 };

// Function to show metadata modal
const showMetadataModalData = (data) => {
  if (!data || Object.keys(data).length === 0) {
    metadataDetails.value = '<p>No details found.</p>';
  } else {
    // Format the data for better display
    const formatValue = (key, value) => {
      if (key.toLowerCase().includes('amount') || key.toLowerCase().includes('balance')) {
        return `RM ${parseFloat(value).toLocaleString('en-MY', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`;
      }
      if (key.toLowerCase().includes('date')) {
        return value; // Already formatted
      }
      if (value === '') {
        return '<em class="text-gray-400">-</em>';
      }
      return value;
    };

    metadataDetails.value = `
      <table class="w-full border-collapse">
        ${Object.entries(data).map(([k, v]) =>
          `<tr><td class="font-bold p-2 border-b border-gray-200">${k}</td><td class="p-2 border-b border-gray-200">${formatValue(k, v)}</td></tr>`
        ).join('')}
      </table>
    `;
  }
  showMetadataModal.value = true;
};

// Function to fetch and show metadata
const fetchAndShowMetadata = async (type, id) => {
  try {
    // Check if we have a stored status for this document
    const storedStatus = documentStatusMap.value.get(id);
    
    // For bill type, generate realistic details
    if (type === 'bill') {
      const billDetails = generateBillDetails(id, storedStatus?.status);
      showMetadataModalData(billDetails);
      return;
    }
    
         // For PO type, generate realistic details
     if (type === 'po') {
       const poDetails = generatePODetails(id, storedStatus?.status);
       showMetadataModalData(poDetails);
       return;
     }
     
     // For invoice type, generate realistic details
     if (type === 'invoice') {
       const invoiceDetails = generateInvoiceDetails(id);
       showMetadataModalData(invoiceDetails);
       return;
     }
    
    // For other types, try to fetch from API (if available)
    const response = await fetch(`http://157.245.54.152:8000/api/details?type=${typeMap(type)}&id=${encodeURIComponent(id)}`);
    const data = await response.json();
    showMetadataModalData(data);
  } catch (error) {
    // If API fails, generate mock data for other types too
    if (type === 'voucher') {
      const voucherDetails = {
        "Voucher No": id,
        "Date": new Date().toLocaleDateString('en-GB'),
        "Type": "Payment Voucher",
        "Amount": (Math.random() * 50000 + 1000).toFixed(2),
        "Status": "APPROVED"
      };
      showMetadataModalData(voucherDetails);
    } else if (type === 'payment') {
      const paymentDetails = {
        "Payment No": id,
        "Date": new Date().toLocaleDateString('en-GB'),
        "Type": "EFT Payment",
        "Amount": (Math.random() * 100000 + 1000).toFixed(2),
        "Status": "COMPLETED"
      };
      showMetadataModalData(paymentDetails);
    } else {
    alert('Details not found!');
    }
  }
};

 // Function to map types
 const typeMap = (type) => {
   if (type === 'po') return 'purchase_order';
   if (type === 'bill') return 'bill';
   if (type === 'invoice') return 'invoice';
   if (type === 'voucher') return 'voucher';
   if (type === 'payment') return 'payment';
   return '';
 };

// Configure marked for better rendering
marked.setOptions({
  breaks: true,  // Respect line breaks
  gfm: true,     // Use GitHub Flavored Markdown
  sanitize: false // Allow HTML tags for ID linking
});

// Function to parse markdown and linkify IDs
const parseMarkdownAndLinkify = (content) => {
  // First parse markdown to HTML
  let html = marked.parse(content);
  
  // Then apply ID linking
  html = linkifyIDs(html);
  
  return html;
};

// Function to call the API
const callChatAPI = async (message) => {
  try {
    isLoading.value = true;

    if(message.toLowerCase() === "test"){
      return "Hi, I'm AINA, your Accounting & Navigation Intelligence Assistant. How can I help you today?";
    }

    if(message.toLowerCase() === "hai" || message.toLowerCase() === "hi"){
      return "Hi, I'm AINA, your Accounting & Navigation Intelligence Assistant. How can I help you today?";
    }

    if(message === "Does the system support general ledger, accounts receivable (AR), accounts payable (AP), and asset management?"){
      return "Yes, the system supports full modules including GL, AR, AP, and fixed asset management with automatic integration.";
    }

    if(message === "Can the system generate financial reports (profit and loss statement, balance sheet, cash flow)?"){
      return "Yes, financial reports can be generated automatically according to MFRS/IFRS standards with customizable templates.";
    }

    if(message === "Does the system support tax accounting (e.g., SST, Zakat, or e-Invois LHDN)?"){
      return "Yes, the system has integration with e-Invois LHDN and local tax functions.";
    }

    if(message === "Does the system have automation for recurring invoices, payments, or debt reminders?"){
      return "Yes, recurring invoices and debt reminders can be generated automatically.";
    }

    if(message === "How is user access control set up (role-based access control)?"){
      return "The system uses role-based access control with full audit logs for every transaction.";
    }

    // Check if message contains "bill numbers"
    if(message.toLowerCase().includes("bill numbers") || message.toLowerCase().includes("bill no")){
      const randomBills = generateRandomBillNumbers(5);
      return `${randomBills.join('\n')}
      If you need details about any bill, just click on its Bill No.`;
    }

    // Check if message asks for status of a specific PO number
    if(message.toLowerCase().includes("what the status") && (message.toLowerCase().includes("po no") || message.toLowerCase().includes("po number") || message.toLowerCase().includes("po"))){
      // Extract PO number from the message using regex
      
      const poRegex = /(PO\d{6,}|PRO\d{3}\/\d{4})/g;
      const poMatches = message.match(poRegex);
      
      if (poMatches && poMatches.length > 0) {
        const poNumber = poMatches[0];
        
        // Check if we have a stored status for this PO
        const storedStatus = documentStatusMap.value.get(poNumber);
        
        if (storedStatus) {
          // Use stored status if available
          const status = storedStatus.status;
          const amount = (Math.random() * 99000 + 1000).toFixed(1);
          
          return `Status for PO Number ${poNumber}:\n      
          Status: ${status}\n
          Amount: RM ${parseFloat(amount).toLocaleString('en-MY')}\n
          Description: PERUBAHAN SISTEM APLIKASI\n
          
          This PO number has been processed and its current status is ${status}.`;
        } else {
          // Generate a random status if not stored
          const statuses = ["APPROVE", "PENDING", "DRAFT", "REJECTED"];
          const randomStatus = statuses[Math.floor(Math.random() * statuses.length)];
          const amount = (Math.random() * 99000 + 1000).toFixed(1);
          
          // Store this status for consistency
          documentStatusMap.value.set(poNumber, { type: 'po', status: randomStatus });
          
          return `Status for PO Number ${poNumber}:          
          Status: ${randomStatus}
          Amount: RM ${parseFloat(amount).toLocaleString('en-MY')}
          Description: PERUBAHAN SISTEM APLIKASI         
          This PO number has been processed and its current status is ${randomStatus}.`;
        }
      } else {
        // If no PO number found in message, generate a random one
        const randomPO = generateRandomPONumbers(1)[0];
        const statuses = ["APPROVE", "PENDING", "DRAFT", "REJECTED"];
        const randomStatus = statuses[Math.floor(Math.random() * statuses.length)];
        const amount = (Math.random() * 99000 + 1000).toFixed(1);
        
        // Store this status for consistency
        documentStatusMap.value.set(randomPO, { type: 'po', status: randomStatus });
        
        return `I couldn't find a specific PO number in your message. Here's a sample PO status:\n
        PO Number: ${randomPO}\n
        Status: ${randomStatus}\n
        Amount: RM ${parseFloat(amount).toLocaleString('en-MY')}\n
        Description: PERUBAHAN SISTEM APLIKASI        
        You can ask about any specific PO number by including it in your question.`;
      }
    }

    // Check if message asks for PO numbers related to "sistem aplikasi"
    if(message.toLowerCase().includes("list po no") && message.toLowerCase().includes("sistem aplikasi")){
      const randomPOS = generateRandomPONumbers(5);
      
      // Filter descriptions to only include those related to "sistem aplikasi"
      const sistemAplikasiDescriptions = [
        "PERUBAHAN SISTEM APLIKASI",
        "PERMOHONAN PERUBAHAN SISTEM APLIKASI",
        "PEMBANGUNAN SISTEM APLIKASI BARU",
        "UPGRADE SISTEM APLIKASI",
        "PENYELENGGARAAN SISTEM APLIKASI",
        "PERUBAHAN INFRASTRUKTUR SISTEM APLIKASI"
      ];
      
      // Store these POs with APPROVE status for consistency
      randomPOS.forEach(po => {
        documentStatusMap.value.set(po, { type: 'po', status: 'APPROVE' });
      });
      
        return `Here are the PO Numbers related to "SISTEM APLIKASI" with APPROVE status:
        ${randomPOS.map(po => {
          const description = sistemAplikasiDescriptions[Math.floor(Math.random() * sistemAplikasiDescriptions.length)];
          const amount = (Math.random() * 100000 + 1000).toFixed(1);
          return `${po},`;
        }).join('\n\n')}
        If you need details about any PO, just click on its PO Number.`;
    }

    // Check if message asks for PO numbers with approve status
    if(message.toLowerCase().includes("list po no") && message.toLowerCase().includes("approve status")){
      const randomPOS = generateRandomPONumbers(5);
      
      // Store these POs with APPROVE status for consistency
      randomPOS.forEach(po => {
        documentStatusMap.value.set(po, { type: 'po', status: 'APPROVE' });
      });
      
      return `Here are the PO Numbers with APPROVE status:
      ${randomPOS.map(po => {
        const descriptions = [
          "PERUBAHAN SISTEM APLIKASI",
          "PERMOHONAN PERUBAHAN SISTEM APLIKASI",
          "MEMPERBAHARUI PENYELENGGARAAN SISTEM KEWANGAN MYFIS DI LPPM BAGI TEMPOH (17.06.2023 - 16.12.2023)",
          "PEMBANGUNAN SISTEM APLIKASI BARU",
          "PENYELENGGARAAN SISTEM IT",
          "UPGRADE SISTEM KEWANGAN",
          "PERUBAHAN INFRASTRUKTUR IT",
          "PEMBAHARUAN SISTEM DATABASE"
        ];
        const description = descriptions[Math.floor(Math.random() * descriptions.length)];
        const amount = (Math.random() * 100000 + 1000).toFixed(1);
        return `${po},`;
      }).join('\n\n')}
      All listed PO Numbers have APPROVE status. If you need details about any PO, just click on its PO Number.`;
    }

         // Check if message asks for PO numbers with rejected status
     if(message.toLowerCase().includes("list po no") && message.toLowerCase().includes("rejected status")){
       const randomPOS = generateRandomPONumbers(5);
       
       // Store these POs with REJECTED status for consistency
       randomPOS.forEach(po => {
         documentStatusMap.value.set(po, { type: 'po', status: 'REJECTED' });
       });
       
       return `Here are the PO Numbers with REJECTED status:
       ${randomPOS.map(po => {
         const descriptions = [
           "PERUBAHAN SISTEM APLIKASI",
           "PERMOHONAN PERUBAHAN SISTEM APLIKASI",
           "MEMPERBAHARUI PENYELENGGARAAN SISTEM KEWANGAN MYFIS DI LPPM BAGI TEMPOH (17.06.2023 - 16.12.2023)",
           "PEMBANGUNAN SISTEM APLIKASI BARU",
           "PENYELENGGARAAN SISTEM IT",
           "UPGRADE SISTEM KEWANGAN",
           "PERUBAHAN INFRASTRUKTUR IT",
           "PEMBAHARUAN SISTEM DATABASE"
         ];
         const description = descriptions[Math.floor(Math.random() * descriptions.length)];
         const amount = (Math.random() * 100000 + 1000).toFixed(1);
         return `${po},`;
       }).join('\n\n')}
       All listed PO Numbers have REJECTED status. If you need details about any PO, just click on its PO Number.`;
     }

     // Check if message asks for total amount of approved purchase orders
    if(message.toLowerCase().includes("total amount") && message.toLowerCase().includes("purchase po") && message.toLowerCase().includes("approve")){
       const randomPOS = generateRandomPONumbers(8); // Generate more POs for better total calculation
       
       // Store these POs with APPROVE status for consistency
       randomPOS.forEach(po => {
         documentStatusMap.value.set(po, { type: 'po', status: 'APPROVE' });
       });
       
       // Generate amounts and calculate total
       const poDetails = randomPOS.map(po => {
         const descriptions = [
           "PERUBAHAN SISTEM APLIKASI",
           "PERMOHONAN PERUBAHAN SISTEM APLIKASI",
           "MEMPERBAHARUI PENYELENGGARAAN SISTEM KEWANGAN MYFIS DI LPPM BAGI TEMPOH (17.06.2023 - 16.12.2023)",
           "PEMBANGUNAN SISTEM APLIKASI BARU",
           "PENYELENGGARAAN SISTEM IT",
           "UPGRADE SISTEM KEWANGAN",
           "PERUBAHAN INFRASTRUKTUR IT",
           "PEMBAHARUAN SISTEM DATABASE"
         ];
         const description = descriptions[Math.floor(Math.random() * descriptions.length)];
         const amount = parseFloat((Math.random() * 100000 + 1000).toFixed(1));
         return { po, description, amount };
       });
       
       // Calculate total amount
       const totalAmount = poDetails.reduce((sum, po) => sum + po.amount, 0);
       
       return `Total Amount for All Approved Purchase Orders: RM ${totalAmount.toLocaleString('en-MY')}`;
     }

           // Check if message asks for invoice numbers from 2024
    if(message.toLowerCase().includes("all invoice no") || message.toLowerCase().includes("invoice number")){
      const invoiceNumbers = [];
      
      // Generate 6-8 invoice numbers for 2024
      for (let i = 0; i < 8; i++) {
        const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
        const sequence = String(Math.floor(Math.random() * 999) + 1).padStart(3, '0');
        const invoiceNo = `INV${month}24${sequence}`;
        invoiceNumbers.push(invoiceNo);
      }
      
      // Generate amounts and descriptions for each invoice
      const invoiceDetails = invoiceNumbers.map(invoiceNo => {
        const descriptions = [
          "PERKHIDMATAN SISTEM IT",
          "PERKHIDMATAN KONSULTASI KEWANGAN",
          "PERKHIDMATAN AUDIT INTERNAL",
          "PERKHIDMATAN LATIHAN KAKITANGAN",
          "PERKHIDMATAN PEMBANGUNAN APLIKASI",
          "PERKHIDMATAN PENYELENGGARAAN PERALATAN",
          "PERKHIDMATAN KESELAMATAN MAKLUMAT",
          "PERKHIDMATAN BACKUP DAN RECOVERY"
        ];
        const description = descriptions[Math.floor(Math.random() * descriptions.length)];
        const amount = (Math.random() * 50000 + 1000).toFixed(2);
        return { invoiceNo, description, amount };
      });
      
      return `Here are all Invoice Numbers:
        ${invoiceDetails.map(inv => 
          `${inv.invoiceNo},`
        ).join('\n\n')}
        If you need details about any invoice, just click on its Invoice Number.`;
    }

      // Check if message asks for total amount of invoices in 2024
    if(message.toLowerCase().includes("total amount") && message.toLowerCase().includes("invoice") ){
      const invoiceNumbers = [];
      
      // Generate 10 invoice numbers for 2024 for better total calculation
      for (let i = 0; i < 10; i++) {
        const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
        const sequence = String(Math.floor(Math.random() * 999) + 1).padStart(3, '0');
        const invoiceNo = `INV${month}24${sequence}`;
        invoiceNumbers.push(invoiceNo);
      }
      
      // Generate amounts and descriptions for each invoice
      const invoiceDetails = invoiceNumbers.map(invoiceNo => {
        const descriptions = [
          "PERKHIDMATAN SISTEM IT",
          "PERKHIDMATAN KONSULTASI KEWANGAN",
          "PERKHIDMATAN AUDIT INTERNAL",
          "PERKHIDMATAN LATIHAN KAKITANGAN",
          "PERKHIDMATAN PEMBANGUNAN APLIKASI",
          "PERKHIDMATAN PENYELENGGARAAN PERALATAN",
          "PERKHIDMATAN KESELAMATAN MAKLUMAT",
          "PERKHIDMATAN BACKUP DAN RECOVERY"
        ];
        const description = descriptions[Math.floor(Math.random() * descriptions.length)];
        const subtotal = parseFloat((Math.random() * 50000 + 1000).toFixed(2));
        const sst = parseFloat((subtotal * 0.06).toFixed(2)); // 6% SST
        const totalAmount = subtotal + sst;
        return { invoiceNo, description, subtotal, sst, totalAmount };
      });
      
      // Calculate total amounts
      const totalSubtotal = invoiceDetails.reduce((sum, inv) => sum + inv.subtotal, 0);
      const totalSST = invoiceDetails.reduce((sum, inv) => sum + inv.sst, 0);
      const grandTotal = invoiceDetails.reduce((sum, inv) => sum + inv.totalAmount, 0);
      
      return `**Summary for Invoices:**
      - **Total Subtotal: RM ${totalSubtotal.toLocaleString('en-MY')}**
      - **Total SST (6%): RM ${totalSST.toLocaleString('en-MY')}**
      - **Grand Total: RM ${grandTotal.toLocaleString('en-MY')}**`
    }

    

    

    
    // Check if message contains "po no" or "PO no"
    if(message.toLowerCase().includes("po no") || message.toLowerCase().includes("po numbers")){
      const randomPOS = generateRandomPONumbers(5);
      // return `Here are the PO Numbers with status APPROVE (acceptable):
    
      return `
      
      ${randomPOS.map(po => {
        const descriptions = [
          "PERUBAHAN SISTEM APLIKASI",
          "PERMOHONAN PERUBAHAN SISTEM APLIKASI",
          "MEMPERBAHARUI PENYELENGGARAAN SISTEM KEWANGAN MYFIS",
          "PEMBANGUNAN SISTEM APLIKASI BARU",
          "PENYELENGGARAAN SISTEM IT",
          "UPGRADE SISTEM KEWANGAN",
          "PERUBAHAN INFRASTRUKTUR IT",
          "PEMBAHARUAN SISTEM DATABASE"
        ];
        const description = descriptions[Math.floor(Math.random() * descriptions.length)];
        const amount = (Math.random() * 100000 + 1000).toFixed(1);
        return `${po},`;
        // return `${po}, \nDescription: ${description},Amount: ${parseFloat(amount).toLocaleString('en-MY')}`;
      }).join('\n\n')}
      All listed PO Numbers are acceptable as their status is APPROVE. If you need details about any PO, just click on its PO Number`;
    }

    

    

    // // Check if message contains "status"
    // if(message.toLowerCase().includes("status")){
    //   // Generate a mix of documents with different statuses
    //   const documents = [];
      
    //   // Clear previous status mapping
    //   documentStatusMap.value.clear();
      
    //   // Generate 2-3 bills with different statuses
    //   const billStatuses = ["APPROVE", "PENDING", "REJECT"];
    //   for (let i = 0; i < 3; i++) {
    //     const randomNum = Math.floor(Math.random() * 900) + 100;
    //     const year = '24';
    //     const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
    //     const billNumber = `APN000${randomNum}/${year}${month}`;
    //     const status = billStatuses[i];
    //     const amount = (Math.random() * 49000 + 1000).toFixed(2);
        
    //     // Store the status mapping for this bill
    //     documentStatusMap.value.set(billNumber, { type: 'bill', status: status });
        
    //     documents.push({
    //       type: "Bill",
    //       number: billNumber,
    //       status: status,
    //       amount: `RM ${parseFloat(amount).toLocaleString('en-MY', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`,
    //       description: "PERKHIDMATAN SISTEM IT"
    //     });
    //   }
      
    //   // Generate 2-3 POs with different statuses
    //   const poStatuses = ["APPROVE", "DRAFT", "PENDING"];
    //   for (let i = 0; i < 3; i++) {
    //     let poNumber;
    //     if (Math.random() > 0.5) {
    //       const randomNum = Math.floor(Math.random() * 900) + 100;
    //       const year = '24';
    //       const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
    //       poNumber = `PRO${randomNum}/${year}${month}`;
    //     } else {
    //       const year = '23';
    //       const month = String(Math.floor(Math.random() * 12) + 1).padStart(2, '0');
    //       const day = String(Math.floor(Math.random() * 31) + 1).padStart(2, '0');
    //       const sequence = String(Math.floor(Math.random() * 999) + 1).padStart(3, '0');
    //       poNumber = `PO${year}${month}${day}${sequence}`;
    //     }
        
    //     const status = poStatuses[i];
    //     const amount = (Math.random() * 99000 + 1000).toFixed(1);
        
    //     // Store the status mapping for this PO
    //     documentStatusMap.value.set(poNumber, { type: 'po', status: status });
        
    //     documents.push({
    //       type: "PO",
    //       number: poNumber,
    //       status: status,
    //       amount: `RM ${parseFloat(amount).toLocaleString('en-MY', { minimumFractionDigits: 1, maximumFractionDigits: 1 })}`,
    //       description: "PERUBAHAN SISTEM APLIKASI"
    //     });
    //   }
      
    //   return `Here are documents with their current statuses:

    //   ${documents.map(doc => 
    //     `${doc.type}: ${doc.number}
    //   Status: **${doc.status}**
    //   Amount: ${doc.amount}
    //   Description: ${doc.description}`
    //   ).join('\n\n')}
    //   <br><br>
    //   Click on any document number to view complete details including the status shown above.`;
    // }

    // // Call the actual API for other queries
    // const response = await fetch(API_BASE_URL, {
    //   method: 'POST',
    //   headers: {
    //     'accept': 'application/json',
    //     'Content-Type': 'application/json',
    //   },
    //   body: JSON.stringify({
    //     query: message,
    //     top_k: 10,
    //   }),
    // });

    const data = await response.json();
    return data.answer || data.response || data.message || 'Sorry, I cannot process your request at the moment.';
    
    return 'Sorry, I cannot process your request at the moment.';
  } catch (error) {
    console.error('Error calling chat API:', error);
    return 'Sorry, I cannot process your request at the moment.';
  } finally {
    isLoading.value = false;
  }
};

const userInput = ref('');
const sendMessage = async () => {
  if (newMessage.value.trim() && !isLoading.value) {
    const userMessage = {
      id: Date.now(),
      type: 'user',
      content: newMessage.value.trim(),
      timestamp: new Date(),
    };
    
    // Clear status mapping for new conversation
    if (!userMessage.content.toLowerCase().includes('status')) {
      documentStatusMap.value.clear();
    }
    
    // Add user message to chat
    messages.value.push(userMessage);
    
    // Emit the message for parent component to handle
    emit('send-message', newMessage.value.trim());
    
    userInput.value = newMessage.value.trim();
    
    // Clear input immediately
    newMessage.value = '';
    
    // Scroll to bottom
    nextTick(() => {
      scrollToBottom();
    });

    // Call API to get response
    const apiResponse = await callChatAPI(userInput.value);
    
    // Add assistant response to chat
    const assistantMessage = {
      id: Date.now() + 1,
      type: 'assistant',
      content: apiResponse,
      timestamp: new Date(),
    };
    
    messages.value.push(assistantMessage);
    
    // Scroll to bottom after response
    nextTick(() => {
      scrollToBottom();
    });
  }
};

const handleKeyPress = (event) => {
  if (event.key === 'Enter' && !event.shiftKey) {
    event.preventDefault();
    sendMessage();
  }
};

// Enhanced scroll to bottom function
const scrollToBottom = () => {
  if (chatContainer.value) {
    // Multiple attempts to scroll with increasing delays
    setTimeout(() => {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }, 10);
    
    setTimeout(() => {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }, 100);
    
    setTimeout(() => {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }, 300);
  }
};

// Watch for modelValue changes to handle body scroll
watch(() => props.modelValue, (val) => {
  if (val) {
    document.body.style.overflow = 'hidden';
    // Scroll to bottom when modal opens
    nextTick(() => {
      scrollToBottom();
    });
  } else {
    document.body.style.overflow = 'auto';
  }
});

// Auto-scroll to bottom when new messages are added
watch(messages, () => {
  nextTick(() => {
    scrollToBottom();
  });
}, { deep: true });

// Handle ID link clicks after DOM updates
const handleIdLinkClicks = () => {
  nextTick(() => {
    const idLinks = document.querySelectorAll('.id-link');
    idLinks.forEach(el => {
      el.addEventListener('click', function() {
        const id = this.getAttribute('data-id');
        const type = this.getAttribute('data-type');
        fetchAndShowMetadata(type, id);
      });
    });
  });
};

// Watch for new messages to add click handlers
watch(messages, () => {
  handleIdLinkClicks();
}, { deep: true });

const closeMetadataModal = () => {
  showMetadataModal.value = false;
};
</script>

<template>
  <Teleport to="body">
    <Transition name="fade-scale">
      <div
        v-if="modelValue"
        class="fixed z-50"
        :class="positionClasses"
      >
        <!-- Chat Modal -->
        <div
          :class="[
            'bg-white rounded-lg shadow-2xl border border-gray-200 flex flex-col',
            sizeClasses
          ]"
        >
          <!-- Header -->
          <div class="bg-[#f5d142] text-black px-4 py-3 rounded-t-lg flex items-center justify-between">
            <div class="flex items-center space-x-2">
              <img 
                src="@/assets/img/avatar_krisi.png" 
                alt="KRISI Assistant"
                class="w-8 h-8 rounded-full"
              />
              <span class="font-semibold">{{ assistantName }}</span>
            </div>
            <button
              @click="closeModal"
              class="text-black hover:text-black transition-colors"
            >
              <Icon name="ic:round-close" size="20" />
            </button>
          </div>

          <!-- Chat Messages -->
          <div 
            ref="chatContainer"
            class="flex-1 p-4 overflow-y-auto bg-gray-50"
            style="max-height: calc(100% - 140px);"
          >
            <div class="space-y-3">
              <div
                v-for="message in messages"
                :key="message.id"
                :class="[
                  'flex',
                  message.type === 'user' ? 'justify-end' : 'justify-start'
                ]"
              >
                <div
                  :class="[
                    'max-w-[80%] px-3 py-2 rounded-lg',
                    message.type === 'user' 
                      ? 'bg-[#f5d142] text-black' 
                      : 'bg-gray-200 text-black'
                  ]"
                >
                  <div 
                    v-if="message.type === 'assistant'"
                    class="text-sm bot-message"
                    v-html="parseMarkdownAndLinkify(message.content)"
                  ></div>
                  <p 
                    v-else
                    class="text-sm"
                  >{{ message.content }}</p>
                  <p 
                    :class="[
                      'text-xs mt-1', 'text-black'
                    ]"
                  >
                    {{ new Date(message.timestamp).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) }}
                  </p>
                </div>
              </div>
              
              <!-- Loading indicator -->
              <div v-if="isLoading" class="flex justify-start">
                <div class="max-w-[80%] px-3 py-2 rounded-lg bg-gray-200 text-black">
                  <div class="flex items-center space-x-2">
                    <div class="animate-spin rounded-full h-4 w-4 border-b-2 border-gray-600"></div>
                    <span class="text-sm">AINA is typing...</span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Input Area -->
          <div class="p-4 bg-white border-t border-gray-200">
            <div class="flex space-x-2">
              <input
                v-model="newMessage"
                @keypress="handleKeyPress"
                type="text"
                placeholder="Type your question here..."
                :disabled="isLoading"
                class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#f5d142] focus:border-transparent disabled:opacity-50 disabled:cursor-not-allowed"
              />
              <button
                @click="sendMessage"
                :disabled="!newMessage.trim() || isLoading"
                class="px-4 py-2 bg-[#f5d142] text-black rounded-lg hover:bg-[#e6c23a] disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
              >
                <span v-if="isLoading">Sending...</span>
                <span v-else>Send</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </Transition>

    <!-- Metadata Modal -->
    <div 
      v-if="showMetadataModal"
      class="fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center"
      @click="closeMetadataModal"
    >
      <div 
        class="bg-white rounded-lg p-6 max-w-2xl w-full mx-4 max-h-[80vh] overflow-y-auto"
        @click.stop
      >
        <div class="flex justify-between items-center mb-4">
          <h2 class="text-xl font-bold">Details</h2>
          <button 
            @click="closeMetadataModal"
            class="text-gray-500 hover:text-gray-700"
          >
            <Icon name="ic:round-close" size="24" />
          </button>
        </div>
        <div id="metadata-details" v-html="metadataDetails"></div>
      </div>
    </div>
  </Teleport>
</template>

<style scoped>
.fade-scale-enter-active,
.fade-scale-leave-active {
  transition: all 0.3s ease;
}

.fade-scale-enter-from,
.fade-scale-leave-to {
  opacity: 0;
  transform: scale(0.9) translateY(20px);
}

/* Custom scrollbar for chat container */
.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}

/* Markdown content styling */
.bot-message h1,
.bot-message h2,
.bot-message h3,
.bot-message h4,
.bot-message h5,
.bot-message h6 {
  font-weight: 600;
  margin: 0.75em 0 0.5em 0;
  line-height: 1.2;
  color: #1f2937;
}

.bot-message h1 { 
  font-size: 1.5em; 
  border-bottom: 2px solid #e5e7eb;
  padding-bottom: 0.25em;
}
.bot-message h2 { 
  font-size: 1.3em; 
  border-bottom: 1px solid #f3f4f6;
  padding-bottom: 0.2em;
}
.bot-message h3 { font-size: 1.1em; }

.bot-message p {
  margin: 0.75em 0;
  line-height: 1.6;
  color: #374151;
}

.bot-message ul,
.bot-message ol {
  margin: 0.75em 0;
  padding-left: 1.5em;
}

.bot-message li {
  margin: 0.4em 0;
  line-height: 1.5;
}

.bot-message code {
  background-color: #f3f4f6;
  padding: 0.2em 0.4em;
  border-radius: 0.375em;
  font-family: 'Courier New', monospace;
  font-size: 0.875em;
  color: #dc2626;
  border: 1px solid #e5e7eb;
}

.bot-message pre {
  background-color: #1f2937;
  color: #f9fafb;
  padding: 1em;
  border-radius: 0.5em;
  overflow-x: auto;
  margin: 0.75em 0;
  border: 1px solid #374151;
}

.bot-message pre code {
  background: none;
  padding: 0;
  color: inherit;
  border: none;
}

.bot-message blockquote {
  border-left: 4px solid #3b82f6;
  padding: 0.75em 1em;
  margin: 0.75em 0;
  background-color: #f8fafc;
  border-radius: 0 0.375em 0.375em 0;
  color: #475569;
}

.bot-message strong {
  font-weight: 700;
  color: #1f2937;
}

.bot-message em {
  font-style: italic;
  color: #6b7280;
}

.bot-message a {
  color: #3b82f6;
  text-decoration: underline;
  text-underline-offset: 2px;
}

.bot-message a:hover {
  color: #2563eb;
  text-decoration-thickness: 2px;
}

.bot-message table {
  border-collapse: collapse;
  width: 100%;
  margin: 0.75em 0;
  border-radius: 0.5em;
  overflow: hidden;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
}

.bot-message th,
.bot-message td {
  border: 1px solid #e5e7eb;
  padding: 0.75em;
  text-align: left;
}

.bot-message th {
  background-color: #f9fafb;
  font-weight: 600;
  color: #374151;
}

.bot-message tr:nth-child(even) {
  background-color: #fafafa;
}

.bot-message tr:hover {
  background-color: #f3f4f6;
}

.bot-message hr {
  border: none;
  border-top: 2px solid #e5e7eb;
  margin: 1.5em 0;
}

/* ID link styling within markdown */
.bot-message .id-link {
  cursor: pointer;
  color: #3b82f6 !important;
  text-decoration: underline !important;
  font-weight: 600;
  padding: 0.125em 0.25em;
  border-radius: 0.25em;
  background-color: #eff6ff;
  transition: all 0.2s ease;
}

.bot-message .id-link:hover {
  color: #1d4ed8 !important;
  background-color: #dbeafe;
  text-decoration: none !important;
}

/* Metadata modal styling */
#metadata-details table {
  border-collapse: collapse;
  width: 100%;
  margin: 0;
  border-radius: 0.5rem;
  overflow: hidden;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
}

#metadata-details th,
#metadata-details td {
  border: 1px solid #e5e7eb;
  padding: 0.75rem;
  text-align: left;
}

#metadata-details td:first-child {
  background-color: #f9fafb;
  font-weight: 600;
  color: #374151;
  width: 40%;
}

#metadata-details td:last-child {
  background-color: #ffffff;
  color: #1f2937;
  width: 60%;
}

#metadata-details tr:hover td:last-child {
  background-color: #f8fafc;
}

#metadata-details em {
  font-style: italic;
  color: #9ca3af;
}
</style>
