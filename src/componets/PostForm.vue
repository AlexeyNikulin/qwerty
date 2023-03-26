<template>
    <form @submit.prevent>
        <h4>Создание поста</h4>
        <my-input 
            v-model="post.title"
            type="text" 
            placeholder="Название"/>
        <my-input 
            v-model="post.body"
            type="text" 
            placeholder="Описание"/>

        <my-buttons
            class="btn-create"
            @click="createPost"
        >Создать</my-buttons>
    </form>
</template>

<script>
export default {
    data() {
        return {
            post: {
                id: '',
                title: '',
                body: '',
            },
        }
    },
    methods: {
        createPost() {
            const id = Date.now();
            this.post['id'] = id;
            this.$emit('create', this.post);
            this.post = {
                id: '',
                title: '',
                body: '',
            }
        }
    }
}
</script>

<style scoped>
form {
    display: flex;
    flex-direction: column;
}

.btn-create {
    margin-top: 15px;
    align-self: flex-end;
}
</style>