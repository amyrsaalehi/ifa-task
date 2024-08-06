<template>
    <div class="item-list">
        <h2>Todo List</h2>
        <!-- Form to add new item -->
        <form @submit.prevent="addNewItem">
            <input v-model="newItemTitle" placeholder="Enter new item name" required>
            <button type="submit">Add Item</button>
        </form>

        <!-- Loading and error messages -->
        <p v-if="loading">Loading items...</p>
        <p v-if="error">{{ error }}</p>

        <!-- List of items -->
        <transition-group name="list" tag="ul">
            <li v-for="item in items" :key="item.id">
                <p>{{ item.title }}</p>
                <button @click="removeItem(item.id)">Remove</button>
            </li>
        </transition-group>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import { fetchItems, addItem, removeItem } from '@/services/api';

export default {
    name: 'ItemList',
    setup() {
        const items = ref([]);
        const loading = ref(false);
        const error = ref(null);
        const newItemTitle = ref('');

        // Fetch items when component mounts
        onMounted(async () => {
            try {
                loading.value = true;
                items.value = await fetchItems();

            } catch (err) {
                error.value = 'Failed to fetch items. Please try again later.';
                console.error(err);
            } finally {
                loading.value = false;
            }
        });

        // Add a new item
        const addNewItem = async () => {
            if (!newItemTitle.value.trim()) return;

            try {
                loading.value = true;
                const newItem = await addItem({ title: newItemTitle.value, completed: false, userId: 1 });
                items.value.unshift(newItem);
                newItemTitle.value = ''; // Clear input after adding
            } catch (err) {
                error.value = 'Failed to add item. Please try again.';
                console.error(err);
            } finally {
                loading.value = false;
            }
        };

        // Remove an item
        const removeItemFromList = async (id) => {
            try {
                loading.value = true;
                await removeItem(id);
                items.value = items.value.filter(item => item.id !== id);
            } catch (err) {
                error.value = 'Failed to remove item. Please try again.';
                console.error(err);
            } finally {
                loading.value = false;
            }
        };

        return {
            items,
            loading,
            error,
            newItemTitle,
            addNewItem,
            removeItem: removeItemFromList
        };
    }
};
</script>

<style scoped>
.item-list {
    max-width: 600px;
    margin: 0 auto;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    margin-bottom: 10px;
    background-color: #f0f0f0;
    border-radius: 4px;
}

li p {
    color: #222;
}

form {
    display: flex;
    margin-bottom: 20px;
}

input {
    flex-grow: 1;
    padding: 5px;
    margin-right: 10px;
}

button {
    padding: 5px 10px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

button:hover {
    background-color: #45a049;
}

/* Transition styles */
.list-enter-active,
.list-leave-active {
    transition: all 0.5s;
}

.list-enter-from,
.list-leave-to {
    opacity: 0;
    transform: translateY(30px);
}
</style>