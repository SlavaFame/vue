<template>
    <form @submit.prevent>
        <h4>Создание поста</h4>
        <my-input
            v-focus
            v-model="post.title"
            type="text" 
            placeholder="Название"
        />
        <my-input
            v-model="post.body"
            type="text" 
            placeholder="Описание"
        />
        <my-button 
            @click="createPost"
        >Создать</my-button>
    </form>
</template>

<script>
import MyButton from '@/components/UI/MyButton';
import MyInput from '@/components/UI/MyInput';

export default {
    components: {
        MyButton, MyInput
    },
    data(){
        return {
            post: {
                title: '',
                body: ''
            }
        }
    },
    methods: {
        createPost() {
            this.post.id = Date.now();
            this.$emit('create', this.post, 'second param', '3 par')
            this.post = {
                title:'',
                post: ''
            }
        }
    }
}
</script>

<style scoped>
.form {
    display: flex;
    flex-direction: column;
}
</style>