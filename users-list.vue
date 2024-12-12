<template>
  <div>
    <input
      type="text"
      placeholder="Search by name"
      v-model="searchTerm"
    />
    <button @click="toggleSortOrder">
      Sort by name ({{ sortOrder === 'asc' ? 'Ascending' : 'Descending' }})
    </button>
    <div v-if="loading">
      <p>Loading users...</p>
    </div>
    <ul v-else>
      <li v-for="user in sortedUsers" :key="user.id">
        {{ user.name }} ({{ user.email }})
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, watch, computed, onMounted } from 'vue';

export default {
  name: 'UserList',
  setup() {
    const users = ref([]);
    const searchTerm = ref('');
    const sortOrder = ref('asc');
    const loading = ref(false);

    const fetchUsers = async () => {
      loading.value = true;
      try {
        const response = await fetch(
          `https://jsonplaceholder.typicode.com/users?name_like=${searchTerm.value}`
        );
        const data = await response.json();
        users.value = data;
      } catch (error) {
        console.error('Failed to fetch users:', error);
      } finally {
        loading.value = false;
      }
    };

    watch(searchTerm, fetchUsers, { immediate: true });

    const sortedUsers = computed(() => {
      return [...users.value].sort((a, b) => {
        if (sortOrder.value === 'asc') {
          return a.name.localeCompare(b.name);
        } else {
          return b.name.localeCompare(a.name);
        }
      });
    });

    const toggleSortOrder = () => {
      sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
    };

    return {
      users,
      searchTerm,
      sortOrder,
      sortedUsers,
      loading,
      toggleSortOrder,
    };
  },
};
</script>

<style>
/* Add any styles if necessary */
</style>
