<script setup lang="ts">
    import ProtectedRoute from '@/components/ProtectedRoute.vue';
    import { ref, reactive, onMounted } from 'vue';
    import api from '@/api';

    const notes = ref([]);

    const content = ref("");
    const title = ref("");

    onMounted(() => {
        getNotes();
    });

    const getNotes = async () => {
        try {
            const res = await api.get('/api/notes/')
            notes.value = res.data
            console.log("ESSSSE", notes.value)
        } catch (err) {
            console.error("Error while fetching notes", err)
        }
    };

    const addNote = async () => {
        if (!title.value || !content.value) return;

        try {
            const payload = {
            title: title.value,
            content: content.value,
            };

            const res = await api.post('/api/notes/', payload);

            if (res.status === 201) {
                console.log('Note added');
                title.value = '';
                content.value = '';
                await getNotes(); // refresh the notes list if you have it defined
            } else {
                console.error('Failed to add note');
            }
        } catch (err) {
            console.error('Error adding note:', err);
        }
    };

    const deleteNote = async (id: number) => {
        try {
            const res = await api.delete(`/api/notes/delete/${id}`)
            if (res.status === 204) console.log("Note deleted")
            else console.error("Error deleting note")
        } catch (err) {
            console.error("Error deleting note", err)
        }
    }

    

</script>

<template>
    <ProtectedRoute>
        <form @submit.prevent="addNote" class="note-form">
            <div class="mb-3">
            <label for="title" class="form-label">Title</label>
            <input
                v-model="title"
                type="text"
                id="title"
                class="form-control"
                required
            />
            </div>

            <div class="mb-3">
            <label for="content" class="form-label">Content</label>
            <textarea
                v-model="content"
                id="content"
                class="form-control"
                rows="4"
                required
            ></textarea>
            </div>
            <button type="submit" class="btn btn-primary">Add Note</button>
        </form>
    </ProtectedRoute>
</template>