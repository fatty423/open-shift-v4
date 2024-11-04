<script setup lang="ts">
import { computed } from 'vue';
import { useRouter } from 'vue-router';
import type { Guard } from '@/types/guard';
import Card from 'primevue/card';
import Button from 'primevue/button';
import Rating from 'primevue/rating';
import Tag from 'primevue/tag';

const router = useRouter();
const props = defineProps<{
  guards: Guard[];
  viewMode: 'list' | 'grid';
  loading: boolean;
}>();

const getVerificationStatusColor = (status: string) => {
  switch (status) {
    case 'verified': return 'success';
    case 'pending': return 'warning';
    case 'rejected': return 'danger';
    default: return 'info';
  }
};

const navigateToProfile = (guardId: string) => {
  router.push(`/guards/${guardId}`);
};
</script>

<template>
  <div>
    <!-- Grid View -->
    <div v-if="viewMode === 'grid'" class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <Card v-for="guard in guards" :key="guard.id" class="hover:shadow-lg transition-shadow">
        <template #header>
          <img :src="guard.profilePicture" :alt="guard.firstName" class="w-full h-48 object-cover" />
        </template>
        <template #title>
          <div class="flex items-center justify-between">
            <span>{{ guard.firstName }} {{ guard.lastName }}</span>
            <Tag :severity="getVerificationStatusColor(guard.verificationStatus)">
              {{ guard.verificationStatus }}
            </Tag>
          </div>
        </template>
        <template #subtitle>
          <div class="flex items-center gap-2">
            <Rating :modelValue="guard.rating" readonly :cancel="false" />
            <span class="text-sm text-gray-600">({{ guard.rating }})</span>
          </div>
        </template>
        <template #content>
          <div class="space-y-2">
            <p class="text-gray-600">
              <i class="pi pi-map-marker mr-2"></i>
              {{ guard.location.city }}, {{ guard.location.state }}
            </p>
            <p class="text-gray-600">
              <i class="pi pi-dollar mr-2"></i>
              ${{ guard.payRate }}/hr
            </p>
            <div class="flex flex-wrap gap-1 mt-2">
              <span
                v-for="cert in guard.certifications"
                :key="cert"
                class="px-2 py-1 bg-blue-100 text-blue-800 text-xs rounded-full"
              >
                {{ cert }}
              </span>
            </div>
          </div>
        </template>
        <template #footer>
          <div class="flex gap-2">
            <Button 
              label="View Profile" 
              class="p-button-outlined flex-1"
              @click="navigateToProfile(guard.id)"
            />
            <Button 
              icon="pi pi-envelope" 
              class="p-button-outlined p-button-secondary"
            />
          </div>
        </template>
      </Card>
    </div>

    <!-- List View -->
    <div v-else class="space-y-4">
      <div
        v-for="guard in guards"
        :key="guard.id"
        class="bg-white rounded-lg shadow-sm hover:shadow-md transition-shadow p-4"
      >
        <div class="flex items-center gap-4">
          <img
            :src="guard.profilePicture"
            :alt="guard.firstName"
            class="w-16 h-16 rounded-full object-cover"
          />
          <div class="flex-1">
            <div class="flex items-center justify-between">
              <div>
                <h3 class="text-lg font-medium">
                  {{ guard.firstName }} {{ guard.lastName }}
                </h3>
                <div class="flex items-center gap-2">
                  <Rating :modelValue="guard.rating" readonly :cancel="false" />
                  <span class="text-sm text-gray-600">({{ guard.rating }})</span>
                </div>
              </div>
              <Tag :severity="getVerificationStatusColor(guard.verificationStatus)">
                {{ guard.verificationStatus }}
              </Tag>
            </div>
            <div class="mt-2 flex items-center gap-4 text-sm text-gray-600">
              <span>
                <i class="pi pi-map-marker mr-1"></i>
                {{ guard.location.city }}, {{ guard.location.state }}
              </span>
              <span>
                <i class="pi pi-dollar mr-1"></i>
                ${{ guard.payRate }}/hr
              </span>
            </div>
          </div>
          <div class="flex gap-2">
            <Button 
              label="View Profile" 
              class="p-button-outlined"
              @click="navigateToProfile(guard.id)"
            />
            <Button 
              icon="pi pi-envelope" 
              class="p-button-outlined p-button-secondary"
            />
          </div>
        </div>
      </div>
    </div>

    <!-- Empty State -->
    <div v-if="!loading && guards.length === 0" class="text-center py-8">
      <p class="text-gray-600">No guards found matching your criteria.</p>
    </div>
  </div>
</template>

<style scoped>
:deep(.p-card) {
  @apply h-full;
}

:deep(.p-card-content) {
  @apply p-4;
}
</style>