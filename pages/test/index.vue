<template>
    <div class="p-4">
      <RsCard>
        <template #header>Book Equipment</template>
        <template #body>
          <FormKit
            type="form"
            @submit="handleBooking"
            :actions="false"
          >
            <FormKit
              type="select"
              label="Equipment"
              name="equipment_id"
              :options="equipmentOptions"
              :validation="[['required']]"
            />
            <FormKit
              type="datetime-local"
              label="Start Date & Time"
              name="start_dt"
              :validation="[['required']]"
            />
            <FormKit
              type="datetime-local"
              label="End Date & Time"
              name="end_dt"
              :validation="[['required']]"
            />
            <FormKit
              type="text"
              label="Purpose"
              name="purpose"
              :validation="[['required']]"
            />
            <FormKit
              type="text"
              label="Location"
              name="location"
              :validation="[['required']]"
            />
            <RsButton type="submit" class="mt-4 w-full bg-green-500 text-white">
              Submit Booking
            </RsButton>
          </FormKit>
        </template>
      </RsCard>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  
  const equipmentOptions = ref([
    { value: '1', label: 'Projector' },
    { value: '2', label: 'Laptop' },
  ]);
  
  const handleBooking = (formData) => {
    // Handle booking logic here
    console.log('Booking data:', formData);
  };
  </script>