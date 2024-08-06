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