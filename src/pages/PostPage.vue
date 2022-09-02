<template>
    <div>
        <h1>Страница с постами</h1>
        <my-input
            v-focus
            v-model="searchQuery"
            placeholder="Поиск..."
        >

        </my-input>
        <div class="app__btns">
            <my-button
                @click="showDialog"
            >
            Создать пост
            </my-button>
            <my-select
                v-model="selectedSort"
                :options="sortOptions"
            />
        </div>
        <my-dialog v-model:show="dialogVisible">
            <post-form
                @create="createPost"
            /> 
        </my-dialog>
        <post-list 
            :posts="sortedAndSearchedPosts"
            @remove="removePost"
            v-if="!isPostLoading"
        />
        <div v-else>Идет загрузка...</div>
        <div v-intersection="loadMorePosts" class="observer"></div>
        <!--<div class="page__wrapper">
            <div 
                v-for="pageNumber in totalPage" 
                :key="pageNumber" 
                class="page"
                :class="{
                    'current-page': page === pageNumber,
                }"
                @click="changePage(pageNumber)"
            >
                {{ pageNumber }}
            </div>
        </div>-->
    </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import axios from "axios";

export default {
    components: {
        PostForm, PostList
    },

    data(){
        return {
            posts: [],
            dialogVisible: false,
            modificatorValue: '',
            isPostLoading: false,
            selectedSort: '',
            searchQuery:'',
            page:1,
            limit:10,
            totalPage:0,
            sortOptions: [
                { value: 'title', name: 'По названию' },
                { value: 'body', name: 'По содержимому' },
            ]
        }
    },
    
    methods: {
        createPost(post){
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post){
            this.posts = this.posts.filter(p => p.id !== post.id)
        },
        showDialog(){
            this.dialogVisible = true
        },
        /*changePage(pageNumber){
            this.page = pageNumber;
            this.fetchPosts();
        },*/
        async fetchPosts(){
            try {
                this.isPostLoading = true;
                setTimeout(async() => {
                    const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                        params: {
                            _page: this.page,
                            _limit: this.limit
                        }
                    });
                    //101 / 10 = 11
                    this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit);
                    this.posts = [...this.posts, ...response.data];
                    this.isPostLoading = false;
                }, 1000)

            } catch (error) {
                alert("ошибка")
            }
        },

        async loadMorePosts(){
            try {
                this.page += 1;

                setTimeout(async() => {
                    const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                        params: {
                            _page: this.page,
                            _limit: this.limit
                        }
                    });
                    //101 / 10 = 11
                    this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit);
                    this.posts = response.data;
                    this.isPostLoading = false;
                }, 1000)

            } catch (error) {
                alert("ошибка")
            }
        }
    },
    mounted(){
        this.fetchPosts();
        console.log(this.$refs.observer);
    },
    computed:{
        sortedPosts(){
            return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]));
        },
        sortedAndSearchedPosts(){
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        }
    },
    watch: {
        /*page(){
            this.fetchPosts()
        }*/
    }
}
</script>

<style>
.app__btns {
    display: flex;
    justify-content: space-between;
    align-items:center;
}

.page__wrapper {
    display:flex;
    margin-top:15px;
}

.page {
    border:1px solid black;
    padding:10px;
}

.current-page {
    border: 2px solid teal;
}

.observer {
    height:80px;
    width:80px;
    background:url('https://upload.wikimedia.org/wikipedia/commons/b/b1/Loading_icon.gif?20151024034921') center center / cover no-repeat;
}
</style>